---
title: Android开发集成聊天环信SDK3.x简单开始
comments: true
categories:
  - Develop
  - Android
tags:
  - Android
  - AndroidStudio
  - Chat
  - Easemob
  - SDK
id: 10070
date: 2016-04-07 11:04:07
---

### 前言  
环信已经发部了`SDK3.x`版本，`SDK3.x`相对于`SDK2.x`来说是整个进行了重写，`API`变化还是比较大的，已经熟悉`SDK2.x`的开发者在使用新的`SDK3.x`还是会遇到不少问题的，不过还好官方给出了`SDK2.x`升级`SDK3.x`指南，已经熟悉`SDK2.x`开发者可以根据文档了解`SDK3.x`的变化，新集成的开发者可以直接参考`SDK3.x`进行集成；
这里简单的实现了sdk的初始化以及注册登录和收发消息，不过ui上没有没有去做很好的处理

### 先看效果图
![ec-demo](http://lzan13.qiniudn.com/blog/uploads/images/2016/4/ec-demo.gif)

### 提供一些地址
* 当前项目地址，可以直接 clone 运行  
[EaseChat Github](https://github.com/lzan13/EaseChat)  

* AndroidStudio下载  
[Android官方下载](http://tools.android.com/download/studio/builds/2-0)  
[国内提供 AndroidDevTools](http://androiddevtools.cn/)  

* 模拟器 Genymotion下载  
[Genymotion 官网](http://genymotion.com/)  

* 环信官方文档  
[SDK3.x 文档](http://docs.easemob.com/im/start)  
[SDK3.x API 文档](http://www.easemob.com/apidoc/android/chat3.0/annotated.html)  
[SDK2.x 升级 SDK3.x 文档](http://docs.easemob.com/im/200androidcleintintegration/140upgradetov30)  


### 说下我当前开发环境
这里并不是一定要按照我的配置来，只是说下当前项目开发运行的环境，如果你的开发环境不同可能需要自己修改下项目配置`build.gradle`文件
>AndroidStudio 2.0  
Gradle 2.10（跟随AndroidStudio 一起更新）  
Android SDK Tool 25.1.1  
Android Build-tools 23.0.2  
Android Support 最新  
Genymotion 2.6  

如果你还是用的`Eclipse`，可以下载`AndroidStudio`尝试下，如果你上不了`Android`官网，不懂怎么翻墙可以找下国内开发提供的一些地址

### 开始集成
这次要实现 `SDK的初始化`、`SDK端的注册登录`、`消息的发送和监听`这三步
 

#### SDK的初始化  
这个初始化时在Application里进行的，这里定义了一个方法去初始化环信的SDK，并在其中进行了一些设置
```java
package net.melove.demo.easechat;

import android.app.ActivityManager;
import android.app.Application;
import android.content.Context;
import android.content.pm.PackageManager;

import com.hyphenate.chat.EMClient;
import com.hyphenate.chat.EMOptions;

import java.util.Iterator;
import java.util.List;

/**
 * Created by lz on 2016/4/16.
 * 项目的 Application类，做一些项目的初始化操作，比如sdk的初始化等
 */
public class ECApplication extends Application {

    // 上下文菜单
    private Context mContext;

    // 记录是否已经初始化
    private boolean isInit = false;

    @Override
    public void onCreate() {
        super.onCreate();
        mContext = this;

        // 初始化环信SDK
        initEasemob();
    }

    /**
     *
     */
    private void initEasemob() {
        // 获取当前进程 id 并取得进程名
        int pid = android.os.Process.myPid();
        String processAppName = getAppName(pid);
        /**
         * 如果app启用了远程的service，此application:onCreate会被调用2次
         * 为了防止环信SDK被初始化2次，加此判断会保证SDK被初始化1次
         * 默认的app会在以包名为默认的process name下运行，如果查到的process name不是app的process name就立即返回
         */
        if (processAppName == null || !processAppName.equalsIgnoreCase(mContext.getPackageName())) {
            // 则此application的onCreate 是被service 调用的，直接返回
            return;
        }
        if (isInit) {
            return;
        }
        /**
         * SDK初始化的一些配置
         * 关于 EMOptions 可以参考官方的 API 文档
         * http://www.easemob.com/apidoc/android/chat3.0/classcom_1_1hyphenate_1_1chat_1_1_e_m_options.html
         */
        EMOptions options = new EMOptions();
        // 设置Appkey，如果配置文件已经配置，这里可以不用设置
        // options.setAppKey("lzan13#hxsdkdemo");
        // 设置自动登录
        options.setAutoLogin(true);
        // 设置是否需要发送已读回执
        options.setRequireAck(true);
        // 设置是否需要发送回执，TODO 这个暂时有bug，上层收不到发送回执
        options.setRequireDeliveryAck(true);
        // 设置是否需要服务器收到消息确认
        options.setRequireServerAck(true);
        // 收到好友申请是否自动同意，如果是自动同意就不会收到好友请求的回调，因为sdk会自动处理，默认为true
        options.setAcceptInvitationAlways(false);
        // 设置是否自动接收加群邀请，如果设置了当收到群邀请会自动同意加入
        options.setAutoAcceptGroupInvitation(false);
        // 设置（主动或被动）退出群组时，是否删除群聊聊天记录
        options.setDeleteMessagesAsExitGroup(false);
        // 设置是否允许聊天室的Owner 离开并删除聊天室的会话
        options.allowChatroomOwnerLeave(true);
        // 设置google GCM推送id，国内可以不用设置
        // options.setGCMNumber(MLConstants.ML_GCM_NUMBER);
        // 设置集成小米推送的appid和appkey
        // options.setMipushConfig(MLConstants.ML_MI_APP_ID, MLConstants.ML_MI_APP_KEY);

        // 调用初始化方法初始化sdk
        EMClient.getInstance().init(mContext, options);

        // 设置开启debug模式
        EMClient.getInstance().setDebugMode(true);

        // 设置初始化已经完成
        isInit = true;
    }

    /**
     * 根据Pid获取当前进程的名字，一般就是当前app的包名
     *
     * @param pid 进程的id
     * @return 返回进程的名字
     */
    private String getAppName(int pid) {
        String processName = null;
        ActivityManager activityManager = (ActivityManager) mContext.getSystemService(Context.ACTIVITY_SERVICE);
        List list = activityManager.getRunningAppProcesses();
        Iterator i = list.iterator();
        while (i.hasNext()) {
            ActivityManager.RunningAppProcessInfo info = (ActivityManager.RunningAppProcessInfo) (i.next());
            try {
                if (info.pid == pid) {
                    // 根据进程的信息获取当前进程的名字
                    processName = info.processName;
                    // 返回当前进程名
                    return processName;
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        // 没有匹配的项，返回为null
        return null;
    }
}

```


#### 主界面
app启动后默认会进入到`ECMainActivity`，不过在主界面会先判断一下是否登录成功过，如果没有，就会跳转到登录几面，然后我们调用登录的时候，在登录方法的`onSuccess()`回调中我们进行了界面的跳转，跳转到主界面，在主界面我们可以发起回话；  
看下主界面的详细代码实现：
```java
package net.melove.demo.easechat;

import android.content.Intent;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.text.TextUtils;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.hyphenate.EMCallBack;
import com.hyphenate.chat.EMClient;

public class ECMainActivity extends AppCompatActivity {

    // 发起聊天 username 输入框
    private EditText mChatIdEdit;
    // 发起聊天
    private Button mStartChatBtn;
    // 退出登录
    private Button mSignOutBtn;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // 判断sdk是否登录成功过，并没有退出和被踢，否则跳转到登陆界面
        if (!EMClient.getInstance().isLoggedInBefore()) {
            Intent intent = new Intent(ECMainActivity.this, ECLoginActivity.class);
            startActivity(intent);
            finish();
            return;
        }

        setContentView(R.layout.activity_main);

        initView();
    }

    /**
     * 初始化界面
     */
    private void initView() {

        mChatIdEdit = (EditText) findViewById(R.id.ec_edit_chat_id);

        mStartChatBtn = (Button) findViewById(R.id.ec_btn_start_chat);
        mStartChatBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // 获取我们发起聊天的者的username
                String chatId = mChatIdEdit.getText().toString().trim();
                if (!TextUtils.isEmpty(chatId)) {
                    // 获取当前登录用户的 username
                    String currUsername = EMClient.getInstance().getCurrentUser();
                    if (chatId.equals(currUsername)) {
                        Toast.makeText(ECMainActivity.this, "不能和自己聊天", Toast.LENGTH_SHORT).show();
                        return;
                    }
                    // 跳转到聊天界面，开始聊天
                    Intent intent = new Intent(ECMainActivity.this, ECChatActivity.class);
                    intent.putExtra("ec_chat_id", chatId);
                    startActivity(intent);
                } else {
                    Toast.makeText(ECMainActivity.this, "Username 不能为空", Toast.LENGTH_LONG).show();
                }
            }
        });

        mSignOutBtn = (Button) findViewById(R.id.ec_btn_sign_out);
        mSignOutBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                signOut();
            }
        });
    }

    /**
     * 退出登录
     */
    private void signOut() {
        // 调用sdk的退出登录方法，第一个参数表示是否解绑推送的token，没有使用推送或者被踢都要传false
        EMClient.getInstance().logout(false, new EMCallBack() {
            @Override
            public void onSuccess() {
                Log.i("lzan13", "logout success");
                // 调用退出成功，结束app
                finish();
            }

            @Override
            public void onError(int i, String s) {
                Log.i("lzan13", "logout error " + i + " - " + s);
            }

            @Override
            public void onProgress(int i, String s) {

            }
        });
    }
}
```


#### SDK端的注册登录
SDK初始化做完之后，就是需要进行环信的登录了，登录了才能使用环信的功能，才能收发消息，有不少人经常问，不注册账户能使用么，这是聊天sdk，不注册账户你拿什么聊天呢！  
登录调用`EMClient.getInstance().login(username, password, callback);`此方法是一个异步方法，所以需要设置`EMCallback`回调来接收登录结果；  
注册调用`EMClient.getInstance().createAccount(username, password);`此方法是同步方法，需要自己创建新线程去调用，不能放在UI线程直接调用；  
因为只是个简单的demo，这边把登录和注册都卸载了LoginActivity类里，这个方法中对调用环信sdk的方法返回错误值做了一些判断，具体错误信息可以参考官方文档：  
[环信SDK3.x EMError](http://www.easemob.com/apidoc/android/chat3.0/classcom_1_1hyphenate_1_1_e_m_error.html)

```java
package net.melove.demo.easechat;

import android.app.ProgressDialog;
import android.content.Intent;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.hyphenate.EMCallBack;
import com.hyphenate.EMError;
import com.hyphenate.chat.EMClient;
import com.hyphenate.exceptions.HyphenateException;

public class ECLoginActivity extends AppCompatActivity {

    // 弹出框
    private ProgressDialog mDialog;

    // username 输入框
    private EditText mUsernameEdit;
    // 密码输入框
    private EditText mPasswordEdit;

    // 注册按钮
    private Button mSignUpBtn;
    // 登录按钮
    private Button mSignInBtn;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        initView();
    }

    /**
     * 初始化界面控件
     */
    private void initView() {
        mUsernameEdit = (EditText) findViewById(R.id.ec_edit_username);
        mPasswordEdit = (EditText) findViewById(R.id.ec_edit_password);

        mSignUpBtn = (Button) findViewById(R.id.ec_btn_sign_up);
        mSignUpBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                signUp();
            }
        });

        mSignInBtn = (Button) findViewById(R.id.ec_btn_sign_in);
        mSignInBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                signIn();
            }
        });
    }

    /**
     * 注册方法
     */
    private void signUp() {
        // 注册是耗时过程，所以要显示一个dialog来提示下用户
        mDialog = new ProgressDialog(this);
        mDialog.setMessage("注册中，请稍后...");
        mDialog.show();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    String username = mUsernameEdit.getText().toString().trim();
                    String password = mPasswordEdit.getText().toString().trim();
                    EMClient.getInstance().createAccount(username, password);
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            if (!ECLoginActivity.this.isFinishing()) {
                                mDialog.dismiss();
                            }
                            Toast.makeText(ECLoginActivity.this, "注册成功", Toast.LENGTH_LONG).show();
                        }
                    });
                } catch (final HyphenateException e) {
                    e.printStackTrace();
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            if (!ECLoginActivity.this.isFinishing()) {
                                mDialog.dismiss();
                            }
                            /**
                             * 关于错误码可以参考官方api详细说明
                             * http://www.easemob.com/apidoc/android/chat3.0/classcom_1_1hyphenate_1_1_e_m_error.html
                             */
                            int errorCode = e.getErrorCode();
                            String message = e.getMessage();
                            Log.d("lzan13", String.format("sign up - errorCode:%d, errorMsg:%s", errorCode, e.getMessage()));
                            switch (errorCode) {
                            // 网络错误
                            case EMError.NETWORK_ERROR:
                                Toast.makeText(ECLoginActivity.this, "网络错误 code: " + errorCode + ", message:" + message, Toast.LENGTH_LONG).show();
                                break;
                            // 用户已存在
                            case EMError.USER_ALREADY_EXIST:
                                Toast.makeText(ECLoginActivity.this, "用户已存在 code: " + errorCode + ", message:" + message, Toast.LENGTH_LONG).show();
                                break;
                            // 参数不合法，一般情况是username 使用了uuid导致，不能使用uuid注册
                            case EMError.USER_ILLEGAL_ARGUMENT:
                                Toast.makeText(ECLoginActivity.this, "参数不合法，一般情况是username 使用了uuid导致，不能使用uuid注册 code: " + errorCode + ", message:" + message, Toast.LENGTH_LONG).show();
                                break;
                            // 服务器未知错误
                            case EMError.SERVER_UNKNOWN_ERROR:
                                Toast.makeText(ECLoginActivity.this, "服务器未知错误 code: " + errorCode + ", message:" + message, Toast.LENGTH_LONG).show();
                                break;
                            case EMError.USER_REG_FAILED:
                                Toast.makeText(ECLoginActivity.this, "账户注册失败 code: " + errorCode + ", message:" + message, Toast.LENGTH_LONG).show();
                                break;
                            default:
                                Toast.makeText(ECLoginActivity.this, "ml_sign_up_failed code: " + errorCode + ", message:" + message, Toast.LENGTH_LONG).show();
                                break;
                            }
                        }
                    });
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }

    /**
     * 登录方法
     */
    private void signIn() {
        mDialog = new ProgressDialog(this);
        mDialog.setMessage("正在登陆，请稍后...");
        mDialog.show();
        String username = mUsernameEdit.getText().toString().trim();
        String password = mPasswordEdit.getText().toString().trim();
        EMClient.getInstance().login(username, password, new EMCallBack() {
            /**
             * 登陆成功的回调
             */
            @Override
            public void onSuccess() {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        mDialog.dismiss();

                        // 加载所有会话到内存
                        EMClient.getInstance().chatManager().loadAllConversations();
                        // 加载所有群组到内存，如果使用了群组的话
                        // EMClient.getInstance().groupManager().loadAllGroups();

                        // 登录成功跳转界面
                        Intent intent = new Intent(ECLoginActivity.this, ECMainActivity.class);
                        startActivity(intent);
                        finish();
                    }
                });
            }

            /**
             * 登陆错误的回调
             * @param i
             * @param s
             */
            @Override
            public void onError(final int i, final String s) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        mDialog.dismiss();
                        Log.d("lzan13", "登录失败 Error code:" + i + ", message:" + s);
                        /**
                         * 关于错误码可以参考官方api详细说明
                         * http://www.easemob.com/apidoc/android/chat3.0/classcom_1_1hyphenate_1_1_e_m_error.html
                         */
                        switch (i) {
                        // 网络异常 2
                        case EMError.NETWORK_ERROR:
                            Toast.makeText(ECLoginActivity.this, "网络错误 code: " + i + ", message:" + s, Toast.LENGTH_LONG).show();
                            break;
                        // 无效的用户名 101
                        case EMError.INVALID_USER_NAME:
                            Toast.makeText(ECLoginActivity.this, "无效的用户名 code: " + i + ", message:" + s, Toast.LENGTH_LONG).show();
                            break;
                        // 无效的密码 102
                        case EMError.INVALID_PASSWORD:
                            Toast.makeText(ECLoginActivity.this, "无效的密码 code: " + i + ", message:" + s, Toast.LENGTH_LONG).show();
                            break;
                        // 用户认证失败，用户名或密码错误 202
                        case EMError.USER_AUTHENTICATION_FAILED:
                            Toast.makeText(ECLoginActivity.this, "用户认证失败，用户名或密码错误 code: " + i + ", message:" + s, Toast.LENGTH_LONG).show();
                            break;
                        // 用户不存在 204
                        case EMError.USER_NOT_FOUND:
                            Toast.makeText(ECLoginActivity.this, "用户不存在 code: " + i + ", message:" + s, Toast.LENGTH_LONG).show();
                            break;
                        // 无法访问到服务器 300
                        case EMError.SERVER_NOT_REACHABLE:
                            Toast.makeText(ECLoginActivity.this, "无法访问到服务器 code: " + i + ", message:" + s, Toast.LENGTH_LONG).show();
                            break;
                        // 等待服务器响应超时 301
                        case EMError.SERVER_TIMEOUT:
                            Toast.makeText(ECLoginActivity.this, "等待服务器响应超时 code: " + i + ", message:" + s, Toast.LENGTH_LONG).show();
                            break;
                        // 服务器繁忙 302
                        case EMError.SERVER_BUSY:
                            Toast.makeText(ECLoginActivity.this, "服务器繁忙 code: " + i + ", message:" + s, Toast.LENGTH_LONG).show();
                            break;
                        // 未知 Server 异常 303 一般断网会出现这个错误
                        case EMError.SERVER_UNKNOWN_ERROR:
                            Toast.makeText(ECLoginActivity.this, "未知的服务器异常 code: " + i + ", message:" + s, Toast.LENGTH_LONG).show();
                            break;
                        default:
                            Toast.makeText(ECLoginActivity.this, "ml_sign_in_failed code: " + i + ", message:" + s, Toast.LENGTH_LONG).show();
                            break;
                        }
                    }
                });
            }

            @Override
            public void onProgress(int i, String s) {

            }
        });
    }
}
```


#### 消息的发送和监听
实现消息的接收需要添加`EMMessageListener`消息监听接口，我们在需要监听的地方要实现这个接口，并实现接口里边的几个回调方法：  
>onMessageReceived(List<EMMessage> list)新消息的回调  
onCmdMessageReceived(List<EMMessage> list)新的透传消息回调  
onMessageReadAckReceived(List<EMMessage> list)消息已读回调  
onMessageDeliveryAckReceived(List<EMMessage> list)消息已发送回调  
onMessageChanged(EMMessage message, Object object)消息状态改变回调  
    
下边是聊天界面消息监听与发送的完整实现，代码注释比较详细，不再一一解释
```java
package net.melove.demo.easechat;

import android.os.Handler;
import android.os.Message;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.text.TextUtils;
import android.text.method.ScrollingMovementMethod;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

import com.hyphenate.EMCallBack;
import com.hyphenate.EMMessageListener;
import com.hyphenate.chat.EMClient;
import com.hyphenate.chat.EMCmdMessageBody;
import com.hyphenate.chat.EMConversation;
import com.hyphenate.chat.EMMessage;
import com.hyphenate.chat.EMTextMessageBody;

import java.util.List;

public class ECChatActivity extends AppCompatActivity implements EMMessageListener {

    // 聊天信息输入框
    private EditText mInputEdit;
    // 发送按钮
    private Button mSendBtn;

    // 显示内容的 TextView
    private TextView mContentText;

    // 消息监听器
    private EMMessageListener mMessageListener;
    // 当前聊天的 ID
    private String mChatId;
    // 当前会话对象
    private EMConversation mConversation;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_chat);

        // 获取当前会话的username(如果是群聊就是群id)
        mChatId = getIntent().getStringExtra("ec_chat_id");
        mMessageListener = this;

        initView();
        initConversation();
    }

    /**
     * 初始化界面
     */
    private void initView() {
        mInputEdit = (EditText) findViewById(R.id.ec_edit_message_input);
        mSendBtn = (Button) findViewById(R.id.ec_btn_send);
        mContentText = (TextView) findViewById(R.id.ec_text_content);
        // 设置textview可滚动，需配合xml布局设置
        mContentText.setMovementMethod(new ScrollingMovementMethod());

        // 设置发送按钮的点击事件
        mSendBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String content = mInputEdit.getText().toString().trim();
                if (!TextUtils.isEmpty(content)) {
                    mInputEdit.setText("");
                    // 创建一条新消息，第一个参数为消息内容，第二个为接受者username
                    EMMessage message = EMMessage.createTxtSendMessage(content, mChatId);
                    // 将新的消息内容和时间加入到下边
                    mContentText.setText(mContentText.getText() + "\n" + content + " -> " + message.getMsgTime());
                    // 调用发送消息的方法
                    EMClient.getInstance().chatManager().sendMessage(message);
                    // 为消息设置回调
                    message.setMessageStatusCallback(new EMCallBack() {
                        @Override
                        public void onSuccess() {
                            // 消息发送成功，打印下日志，正常操作应该去刷新ui
                            Log.i("lzan13", "send message on success");
                        }

                        @Override
                        public void onError(int i, String s) {
                            // 消息发送失败，打印下失败的信息，正常操作应该去刷新ui
                            Log.i("lzan13", "send message on error " + i + " - " + s);
                        }

                        @Override
                        public void onProgress(int i, String s) {
                            // 消息发送进度，一般只有在发送图片和文件等消息才会有回调，txt不回调
                        }
                    });
                }
            }
        });
    }

    /**
     * 初始化会话对象，并且根据需要加载更多消息
     */
    private void initConversation() {

        /**
         * 初始化会话对象，这里有三个参数么，
         * 第一个表示会话的当前聊天的 useranme 或者 groupid
         * 第二个是绘画类型可以为空
         * 第三个表示如果会话不存在是否创建
         */
        mConversation = EMClient.getInstance().chatManager().getConversation(mChatId, null, true);
        // 设置当前会话未读数为 0
        mConversation.markAllMessagesAsRead();
        int count = mConversation.getAllMessages().size();
        if (count < mConversation.getAllMsgCount() && count < 20) {
            // 获取已经在列表中的最上边的一条消息id
            String msgId = mConversation.getAllMessages().get(0).getMsgId();
            // 分页加载更多消息，需要传递已经加载的消息的最上边一条消息的id，以及需要加载的消息的条数
            mConversation.loadMoreMsgFromDB(msgId, 20 - count);
        }
        // 打开聊天界面获取最后一条消息内容并显示
        if (mConversation.getAllMessages().size() > 0) {
            EMMessage messge = mConversation.getLastMessage();
            EMTextMessageBody body = (EMTextMessageBody) messge.getBody();
            // 将消息内容和时间显示出来
            mContentText.setText(body.getMessage() + " - " + mConversation.getLastMessage().getMsgTime());
        }
    }

    /**
     * 自定义实现Handler，主要用于刷新UI操作
     */
    Handler mHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            switch (msg.what) {
            case 0:
                EMMessage message = (EMMessage) msg.obj;
                // 这里只是简单的demo，也只是测试文字消息的收发，所以直接将body转为EMTextMessageBody去获取内容
                EMTextMessageBody body = (EMTextMessageBody) message.getBody();
                // 将新的消息内容和时间加入到下边
                mContentText.setText(mContentText.getText() + "\n" + body.getMessage() + " <- " + message.getMsgTime());
                break;
            }
        }
    };

    @Override
    protected void onResume() {
        super.onResume();
        // 添加消息监听
        EMClient.getInstance().chatManager().addMessageListener(mMessageListener);
    }

    @Override
    protected void onStop() {
        super.onStop();
        // 移除消息监听
        EMClient.getInstance().chatManager().removeMessageListener(mMessageListener);
    }
    /**
     * --------------------------------- Message Listener -------------------------------------
     * 环信消息监听主要方法
     */
    /**
     * 收到新消息
     *
     * @param list 收到的新消息集合
     */
    @Override
    public void onMessageReceived(List<EMMessage> list) {
        // 循环遍历当前收到的消息
        for (EMMessage message : list) {
            if (message.getFrom().equals(mChatId)) {
                // 设置消息为已读
                mConversation.markMessageAsRead(message.getMsgId());

                // 因为消息监听回调这里是非ui线程，所以要用handler去更新ui
                Message msg = mHandler.obtainMessage();
                msg.what = 0;
                msg.obj = message;
                mHandler.sendMessage(msg);
            } else {
                // 如果消息不是当前会话的消息发送通知栏通知
            }
        }
    }

    /**
     * 收到新的 CMD 消息
     *
     * @param list
     */
    @Override
    public void onCmdMessageReceived(List<EMMessage> list) {
        for (int i = 0; i < list.size(); i++) {
            // 透传消息
            EMMessage cmdMessage = list.get(i);
            EMCmdMessageBody body = (EMCmdMessageBody) cmdMessage.getBody();
            Log.i("lzan13", body.action());
        }
    }

    /**
     * 收到新的已读回执
     *
     * @param list 收到消息已读回执
     */
    @Override
    public void onMessageReadAckReceived(List<EMMessage> list) {
    }

    /**
     * 收到新的发送回执
     * TODO 无效 暂时有bug
     *
     * @param list 收到发送回执的消息集合
     */
    @Override
    public void onMessageDeliveryAckReceived(List<EMMessage> list) {
    }

    /**
     * 消息的状态改变
     *
     * @param message 发生改变的消息
     * @param object  包含改变的消息
     */
    @Override
    public void onMessageChanged(EMMessage message, Object object) {
    }
}
```


#### 界面布局
界面的实现也是非常简单，这里直接贴一下：  
activity_main.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="net.melove.demo.easechat.ECMainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <EditText
            android:id="@+id/ec_edit_chat_id"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="对方的username"/>

        <Button
            android:id="@+id/ec_btn_start_chat"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="发起聊天"/>

        <Button
            android:id="@+id/ec_btn_sign_out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="退出登录"/>
    </LinearLayout>
</RelativeLayout>
```

activity_login.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="net.melove.demo.easechat.ECLoginActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <EditText
            android:id="@+id/ec_edit_username"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="username"/>

        <EditText
            android:id="@+id/ec_edit_password"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="password"/>

        <Button
            android:id="@+id/ec_btn_sign_up"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="注册"/>

        <Button
            android:id="@+id/ec_btn_sign_in"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="登录"/>
    </LinearLayout>
</RelativeLayout>
```

activity_chat.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="net.melove.demo.easechat.ECChatActivity">

    <!--输入框-->
    <RelativeLayout
        android:id="@+id/ec_layout_input"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true">

        <Button
            android:id="@+id/ec_btn_send"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentRight="true"
            android:text="Send"/>

        <EditText
            android:id="@+id/ec_edit_message_input"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_alignParentLeft="true"
            android:layout_toLeftOf="@id/ec_btn_send"/>
    </RelativeLayout>

    <!--展示消息内容-->
    <TextView
        android:id="@+id/ec_text_content"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_above="@id/ec_layout_input"
        android:maxLines="15"
        android:scrollbars="vertical"/>
</RelativeLayout>
```

#### 别忘了配置文件
最后我们需要配置一些权限，我们的代码才能很好的工作
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest package="net.melove.demo.easechat"
          xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- 项目权限 -->
    <!-- 相机 -->
    <uses-permission android:name="android.permission.CAMERA"/>
    <!-- 录音 -->
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <!-- 震动 -->
    <uses-permission android:name="android.permission.VIBRATE"/>
    <!-- 网络 -->
    <uses-permission android:name="android.permission.INTERNET"/>
    <!-- 访问网络状态 -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <!-- 访问WIFI状态 -->
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <!-- 写入外部存储 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <!-- 访问精确定位 -->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <!-- 修改音频设置 -->
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS"/>
    <!-- 读取手机状态 -->
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
    <!-- 允许读写系统设置项 使用设置时需要 -->
    <uses-permission android:name="android.permission.WRITE_SETTINGS"/>
    <!-- 读取启动设置 -->
    <uses-permission android:name="com.android.launcher.permission.READ_SETTINGS"/>

    <!--非必需权限-->
    <!-- 开机自启动 -->
    <!--<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>-->
    <!--<uses-permission android:name="android.permission.GET_TASKS"/>-->
    <!-- 安装卸载文件系统 -->
    <!--<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>-->
    <!-- 改变WIFI状态 -->
    <!--<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>-->
    <!-- 读取描述文件 -->
    <!--<uses-permission android:name="android.permission.READ_PROFILE"/>-->
    <!-- 读取联系人 -->
    <!--<uses-permission android:name="android.permission.READ_CONTACTS"/>-->

    <!-- 连续广播（允许一个程序收到广播后快速收到下一个广播） -->
    <uses-permission android:name="android.permission.BROADCAST_STICKY"/>

    <application
        android:name=".ECApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".ECMainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>

                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
        <activity android:name=".ECLoginActivity">
        </activity>
        <activity
            android:name=".ECChatActivity"
            android:windowSoftInputMode="adjustResize|stateHidden">
        </activity>


        <!-- 设置环信应用的 appkey 换成自己的-->
        <meta-data
            android:name="EASEMOB_APPKEY"
            android:value="lzan13#hxsdkdemo"/>
        <!-- 声明sdk所需的service SDK核心功能-->
        <service
            android:name="com.hyphenate.chat.EMChatService"
            android:exported="true"/>
        <!-- 声明sdk所需的receiver -->
        <receiver android:name="com.hyphenate.chat.EMMonitorReceiver">
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package"/>
            </intent-filter>
            <!-- 可选filter -->
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="android.intent.action.USER_PRESENT"/>
            </intent-filter>
        </receiver>
    </application>
</manifest>
```

### 结语
代码结束，Coding不止！Coding - Coding - Coding ---  
OK了，一个简单的注册登录以及收发消息的小demo就算完成了，可以用自己的环境编译运行下试试