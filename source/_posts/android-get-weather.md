---
title: Android开发之获取简单的天气信息
categories:
  - Develop
  - Android
tags:
  - Android
  - Json
  - Weather
id: 468
date: 2014-01-01 22:34:11
---

这也是在参考了其他的开发者的情况下写出来的，不是很详细，我这里只是简单记录一下

这里是通过http请求中国气象局获取天气信息的json数据，然后通过解析获得天气信息！

首先是一段json示例数据：

```json
/**
* 通过URL：http://www.weather.com.cn/data/cityinfo/101010100.html 获得的就是下边的数据
* 另外一种是获取更详细的信息的方式：http://m.weather.com.cn/data/101010100.html
* 还有一个是获取实况信息的方式：http://www.weather.com.cn/data/sk/101010100.html
* {
* 	"weatherinfo":
* 	{
* 		"city":"北京",
* 		"cityid":"101010100",
* 		"temp1":"10℃",
* 		"temp2":"-2℃",
* 		"weather":"晴",
* 		"img1":"d0.gif",
* 		"img2":"n0.gif",
* 		"ptime":"11:00"
* 	}
* }
* @param context
*/
```

对于想直接获取天气图片的话，可以用地址：http://www.weather.com.cn/m/i/weatherpic/29x20/+img1,自己拼接！

下边是通过http获取json方法类：
```java
public class AnalysisWeatherTool {

	private Context mContext;

	private String city;

	private String temp1;
	private String temp2;

	private String weather;

	private String img1;
	private String img2;

	private String ptime;

	/**
	 * {
	 * 	"weatherinfo":
	 * 	{
	 * 		"city":"北京",
	 * 		"cityid":"101010100",
	 * 		"temp1":"10℃",
	 * 		"temp2":"-2℃",
	 * 		"weather":"晴",
	 * 		"img1":"d0.gif",
	 * 		"img2":"n0.gif",
	 * 		"ptime":"11:00"
	 * 	}
	 * }
	 * @param context
	 */

	public AnalysisWeatherTool(Context context){
		mContext = context;
	}

	/** 
     * 根据给定的url地址访问网络，得到响应内容(这里为GET方式访问) 
     * @param url 指定的url地址 
     * @return web服务器响应的内容，为`String`类型，当访问失败时，返回为null 
     */  
    public String getWebContent(String url) {  
        //创建一个http请求对象  
        HttpGet httpGet = new HttpGet(url);  
        //创建HttpParams以用来设置HTTP参数  
        HttpParams params=new BasicHttpParams();  
        //设置连接超时或响应超时  
        HttpConnectionParams.setConnectionTimeout(params, 3000);  
        HttpConnectionParams.setSoTimeout(params, 5000);  

        //创建一个网络访问处理对象  
        HttpClient httpClient = new DefaultHttpClient(params);

        int res = 0; 
        try {
			res = httpClient.execute(httpGet).getStatusLine().getStatusCode();
			if (res == 200) { 
	            /* 
	             * 当返回码为200时，做处理 
	             * 得到服务器端返回json数据，并做处理 
	             * */ 
	            HttpResponse httpResponse = httpClient.execute(httpGet); 
	            StringBuilder builder = new StringBuilder(); 
	            BufferedReader bReader = new BufferedReader( 
	                    new InputStreamReader(httpResponse.getEntity().getContent())); 
	            String str2 = ""; 
	            for (String s = bReader.readLine(); 
	            			s != null; 
	            			s = bReader.readLine()) { 
	                builder.append(s); 
	            }

	            //使用JSONObject来解析json数据，方便快速
	            JSONObject jsonObject = new JSONObject(builder.toString()).getJSONObject("weatherinfo");

		        setCity(jsonObject.getString("city"));
		        setTemp1(jsonObject.getString("temp1"));
		        setTemp2(jsonObject.getString("temp2"));
		        setWeather(jsonObject.getString("weather"));
		        setImg1(jsonObject.getString("img1"));
		        setImg2(jsonObject.getString("img2"));
		        setPtime(jsonObject.getString("ptime"));
	        }
		} catch (ClientProtocolException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} catch (JSONException e) {
			e.printStackTrace();
		} 
		return null;
    }
	public String getCity() {
		return city;
	}
	public void setCity(String city) {
		this.city = city;
	}
	public String getTemp1() {
		return temp1;
	}
	public void setTemp1(String temp1) {
		this.temp1 = temp1;
	}
	public String getTemp2() {
		return temp2;
	}
	public void setTemp2(String temp2) {
		this.temp2 = temp2;
	}
	public String getWeather() {
		return weather;
	}
	public void setWeather(String weather) {
		this.weather = weather;
	}
	public String getImg1() {
		return img1;
	}
	public void setImg1(String img1) {
		this.img1 = img1;
	}
	public String getImg2() {
		return img2;
	}
	public void setImg2(String img2) {
		this.img2 = img2;
	}
	public String getPtime() {
		return ptime;
	}
	public void setPtime(String ptime) {
		this.ptime = ptime;
	}
}
```

然后就是去设置显示出来了，把我的主类也贴出来吧：
```java
public class MainActivity extends Activity {

	private TextView cityView;
	private TextView tempView;
	private TextView weatherView;
	private TextView ptimeView;
	private ImageView weatherImg;

	private List<string> cityInfo;
	private List</string><string> cityId;

	private String str;
	private AnalysisWeatherTool weatherInfo;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);

		initView();

	}

	private void initView(){
		cityView = (TextView)findViewById(R.id.city);
		tempView = (TextView)findViewById(R.id.temp);
		weatherView = (TextView)findViewById(R.id.weather);
		ptimeView = (TextView)findViewById(R.id.ptime);
		weatherImg = (ImageView)findViewById(R.id.img);

		new MyThread().start();

	}

	//用新建的线程去请求天气信息，这样不会阻塞UI
	class MyThread extends Thread{
		@Override
		public void run() {
			weatherInfo = new AnalysisWeatherTool(MainActivity.this);
			//101131004   101181001   101131011  
			str = weatherInfo.getWebContent("http://www.weather.com.cn/data/cityinfo/101131011.html");
			Message msg = new Message();
			msg.what = 0;
			handler.sendMessage(msg);
			super.run();
		}

		public void getCityList(){
			cityInfo = new ArrayList</string><string>();
			cityId = new ArrayList</string><string>();

			DBHelperTool db = DBHelperTool.getInstance();
			Cursor c = db.queryAll("city_info", null, null, null, null, null, null);

			if(c.moveToFirst()){
				for(int i=0; i<c .getCount(); i++){
					c.moveToPosition(i);

					String city_id = c.getString(2);
					String city_name = c.getString(5);

					cityInfo.add(city_name);
					cityId.add(city_id);
				}
			}
		}
	}

	Handler handler = new Handler(){
		@Override
		public void handleMessage(Message msg) {
			// TODO Auto-generated method stub
			super.handleMessage(msg);

			switch(msg.what){
			case 0:
				cityView.setText("城市：" + weatherInfo.getCity());
				tempView.setText("气温：" + weatherInfo.getTemp2() + " ~ " + weatherInfo.getTemp1());
				weatherView.setText("天气：" + weatherInfo.getWeather());
				ptimeView.setText("更新时间：" + weatherInfo.getPtime());
				break;
			}
		}
	};

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate(R.menu.main, menu);
		return true;
	}
}
```