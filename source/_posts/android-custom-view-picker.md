---
title: Android自定义控件之选择器(Picker)
categories:
  - Develop
  - Android
tags:
  - Android
  - CustomView
  - Picker
id: 438
date: 2013-12-17 21:38:51
---

最近项目中要用到滚动选择器，搜了好多只找到了一个`NumberPicker`，但是不是我想要的！

就自己动手来写一个了！想想应该不是很难吧！这里只是简单写下我的实现方法，不过效果不是很理想，滑动的时候不是很流畅，做个参考吧！
先看下效果图吧：
[![picker](http://wp-melove.qiniudn.com/blogimg/2013/12/picker.jpg)](http://wp-melove.qiniudn.com/blogimg/2013/12/picker.jpg)
上主要代码，这个类就是自定义的PickerView！

```java
public class MySelectorView extends MyBaseView implements MySelectorControl{

	private Context mContext;
	private int left;
	private int top;
	private int right;
	private int bottom;

	private String[] selectorArray = {"测试内容1","测试内容2","测试内容3","测试内容4"};

	private Paint paint = new Paint();
	private float startX, startY, stopX, stopY;
	private int itemHeight;
	private Rect cRect, bgRect;
	private float positionX, positionY;
	private float defPosition, realPosition;

	public MySelectorView(Context context) {
		super(context);

		mContext = context;

		//因为这个是在我整个项目中用到的，在单个的项目中没有测试，这些位置就先自定义一下吧
		left = 10;
		top = 10;
		right = 466;
		bottom = 453;

		itemHeight = 60;

		paint.setAntiAlias(true);
		paint.setTextAlign(Paint.Align.CENTER);
		paint.setTextSize(30);

		cRect = new Rect(left, top, right, bottom);
		bgRect = new Rect(left, top, right, bottom);

		positionX = (right - left)/2 + left;
		positionY = (bottom - top)/2 + top;

		defPosition = positionY + 15;
		realPosition = defPosition;

	}

	//重写onDraw方法，用来绘制自定义的view
	@Override
	protected void onDraw(Canvas canvas) {
		super.onDraw(canvas);

		canvas.clipRect(cRect);
		// 画背景
		drawBackground(canvas);
		// 画文字
		drawText(canvas);

		drawTopShader(canvas);
		drawBottomShader(canvas);
	}

	private void drawBackground(Canvas canvas){
		//这里画一下背景颜色
		paint.setShader(null);
		paint.setColor(getResources().getColor(R.color.gray_bg));
		paint.setStyle(Style.FILL);
		canvas.drawRect(bgRect, paint);
	}

	private void drawText(Canvas canvas){
		//把那些选项绘制出来
		paint.setShader(null);
		paint.setStyle(Style.FILL_AND_STROKE);
		paint.setARGB(0xFF, 0x00, 0x00, 0x00);
		for(int i=0; i<selectorarray .length; i++){
			canvas.drawText(selectorArray[i], positionX, realPosition + i * itemHeight, paint);
		}
	}

	private void drawTopShader(Canvas canvas){
		/* 设置渐变色 这个正方形的颜色是改变的 */  
		Shader mShader = new LinearGradient(left, top, left, positionY - 30,  
			new int[] {Color.BLACK, Color.GRAY}, null, Shader.TileMode.CLAMP);  
		paint.setShader(mShader);  
		paint.setStyle(Style.FILL);
		paint.setARGB(0xAA, 0x00, 0x00, 0x00);
		canvas.drawRect(new Rect(left, top, right, (int) (positionY - 30)), paint);
		}

		private void drawBottomShader(Canvas canvas){
			Shader mShader = new LinearGradient(left, positionY + 30, left, bottom,  
			new int[] {Color.GRAY, Color.BLACK}, null, Shader.TileMode.CLAMP);  
		paint.setShader(mShader);  
		paint.setStyle(Style.FILL);
		paint.setARGB(0xAA, 0x00, 0x00, 0x00);
		canvas.drawRect(new Rect(left, (int) (positionY + 30), right, bottom), paint);
	}

	//重写onTouchEvent方法，去判断触摸屏事件
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		float x = event.getX();
		float y = event.getY();

		Rect controlRect = new Rect(left, top, right, bottom);

			switch (event.getAction()) {
			case MotionEvent.ACTION_DOWN:
				if (controlRect.contains((int) x, (int) y)) {
					startY = y;
					return true;
				}
				return false;
			case MotionEvent.ACTION_MOVE:
				if (controlRect.contains((int) x, (int) y)) {

					stopY = y;

					realPosition = defPosition + (stopY - startY);

					invalidateDraw(); 
					return true;
				}
				return false;
			case MotionEvent.ACTION_UP:
				if (controlRect.contains((int) x, (int) y)) {
					stopY = y;
				}

				float res = (realPosition - (positionY + 15))%60;
				if(res < 0){
					if(res >= -30){
						realPosition = realPosition - res;
					}else{
						realPosition = realPosition - (60 + res);
					}
				}
				if(realPosition > (positionY +15)){
					realPosition = positionY + 15;
				}
				if(realPosition < positionY + 15 - (selectorArray.length - 1) * itemHeight){
					realPosition = positionY + 15 - (selectorArray.length - 1) * itemHeight;
				}

				defPosition = realPosition ;
				int count = (int) (((positionY + 15) - realPosition)/60);
				break;
			default:
				break;
			}
			invalidateDraw();
		return false;
	}

	private void invalidateDraw(){
		invalidate(left - 5, top - 5, right + 5, bottom + 5);
	}

}
```
由于是在项目中用到的，可以正常使用，没有单独测试！