---
title: Android开发自定义ImageView控件实现圆角边框等功能
date: 2015-04-04 10:58:47
categories:
  - Develop
  - Android
tags:
  - Android
  - CustomView
  - ImageView
id: 901
---

在开发中有时我们需要一个功能，或者一个控件，但是官方的又满足不了我们的需求，此时就需要我们自己实现这些功能；
下边边就是我们经常会需要的一个实现了图片的圆角，以及添加边框等功能的自定义控件；文章最后有项目源码地址

这个自定义`ImageView`控件实现了图片的圆角、圆形、边框等功能，同时具有按下改变颜色的效果，通过属性设置可以自定义按下的颜色，
以及颜色的透明度；还尅定义边框的颜色
Demo截图：
![device-2015-05-04-101609](http://wp-melove.qiniudn.com/blogimg/2015/05/device-2015-05-04-101609.png)

### 控件属性定义
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!--自定义MLImageView的属性 加上了自己的前缀，防止和其他自定义控件冲突-->
    <declare-styleable name="MLImageView">
        <attr name="ml_border_color" format="color" />
        <attr name="ml_border_width" format="dimension" />
        <attr name="ml_press_alpha" format="integer" />
        <attr name="ml_press_color" format="color" />
        <attr name="ml_radius" format="dimension" />
        <attr name="ml_shape_type" format="enum">
            <enum name="none" value="0" />
            <enum name="round" value="1" />
            <enum name="rectangle" value="2" />
        </attr>
    </declare-styleable>
</resources>
```
### 控件代码的实现
```java
package net.melove.demo.chat.widget;

import android.content.Context;
import android.content.res.TypedArray;
import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Matrix;
import android.graphics.Paint;
import android.graphics.PorterDuff;
import android.graphics.PorterDuffXfermode;
import android.graphics.RectF;
import android.graphics.drawable.BitmapDrawable;
import android.graphics.drawable.ColorDrawable;
import android.graphics.drawable.Drawable;
import android.util.AttributeSet;
import android.view.MotionEvent;
import android.widget.ImageView;

import net.melove.demo.chat.R;

/**
 * Created by lzan13 on 2015/4/30.
 * 自定义 ImageView 控件，实现了圆角和边框，以及按下变色
 */
public class MLImageView extends ImageView {
    // 图片按下的画笔
    private Paint pressPaint;
    // 图片的宽高
    private int width;
    private int height;

    // 定义 Bitmap 的默认配置
    private static final Bitmap.Config BITMAP_CONFIG = Bitmap.Config.ARGB_8888;
    private static final int COLORDRAWABLE_DIMENSION = 1;

    // 边框颜色
    private int borderColor;
    // 边框宽度
    private int borderWidth;
    // 按下的透明度
    private int pressAlpha;
    // 按下的颜色
    private int pressColor;
    // 圆角半径
    private int radius;
    // 图片类型（矩形，圆形）
    private int shapeType;

    public MLImageView(Context context) {
        super(context);
        init(context, null);
    }

    public MLImageView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init(context, attrs);
    }

    public MLImageView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init(context, attrs);
    }

    private void init(Context context, AttributeSet attrs) {
        //初始化默认值
        borderWidth = 6;
        borderColor = 0xddffffff;
        pressAlpha = 0x42;
        pressColor = 0x42000000;
        radius = 16;
        shapeType = 2;

        // 获取控件的属性值
        if (attrs != null) {
            TypedArray array = context.obtainStyledAttributes(attrs, R.styleable.MLImageView);
            borderColor = array.getColor(R.styleable.MLImageView_ml_border_color, borderColor);
            borderWidth = array.getDimensionPixelOffset(R.styleable.MLImageView_ml_border_width, borderWidth);
            pressAlpha = array.getInteger(R.styleable.MLImageView_ml_press_alpha, pressAlpha);
            pressColor = array.getColor(R.styleable.MLImageView_ml_press_color, pressColor);
            radius = array.getDimensionPixelOffset(R.styleable.MLImageView_ml_radius, radius);
            shapeType = array.getInteger(R.styleable.MLImageView_ml_shape_type, shapeType);
            array.recycle();
        }

        // 按下的画笔设置
        pressPaint = new Paint();
        pressPaint.setAntiAlias(true);
        pressPaint.setStyle(Paint.Style.FILL);
        pressPaint.setColor(pressColor);
        pressPaint.setAlpha(0);
        pressPaint.setFlags(Paint.ANTI_ALIAS_FLAG);

        setClickable(true);
        setDrawingCacheEnabled(true);
        setWillNotDraw(false);
    }

    @Override
    protected void onDraw(Canvas canvas) {

        if (shapeType == 0) {
            super.onDraw(canvas);
            return;
        }
        // 获取当前控件的 drawable
        Drawable drawable = getDrawable();
        if (drawable == null) {
            return;
        }
        // 这里 get 回来的宽度和高度是当前控件相对应的宽度和高度（在 xml 设置）
        if (getWidth() == 0 || getHeight() == 0) {
            return;
        }
        // 获取 bitmap，即传入 imageview 的 bitmap
        // Bitmap bitmap = ((BitmapDrawable) ((SquaringDrawable)
        // drawable).getCurrent()).getBitmap();
        // 这里参考赵鹏的获取 bitmap 方式，因为上边的获取会导致 Glide 加载的drawable 强转为 BitmapDrawable 出错
        Bitmap bitmap = getBitmapFromDrawable(drawable);
        drawDrawable(canvas, bitmap);

        drawPress(canvas);
        drawBorder(canvas);
    }

    /**
     * 实现圆角的绘制
     *
     * @param canvas
     * @param bitmap
     */
    private void drawDrawable(Canvas canvas, Bitmap bitmap) {
        // 画笔
        Paint paint = new Paint();
        // 颜色设置
        paint.setColor(0xffffffff);
        // 抗锯齿
        paint.setAntiAlias(true);
        //Paint 的 Xfermode，PorterDuff.Mode.SRC_IN 取两层图像的交集部门, 只显示上层图像。
        PorterDuffXfermode xfermode = new PorterDuffXfermode(PorterDuff.Mode.SRC_IN);
        // 标志
        int saveFlags = Canvas.MATRIX_SAVE_FLAG
                | Canvas.CLIP_SAVE_FLAG
                | Canvas.HAS_ALPHA_LAYER_SAVE_FLAG
                | Canvas.FULL_COLOR_LAYER_SAVE_FLAG
                | Canvas.CLIP_TO_LAYER_SAVE_FLAG;
        canvas.saveLayer(0, 0, width, height, null, saveFlags);

        if (shapeType == 1) {
            // 画遮罩，画出来就是一个和空间大小相匹配的圆（这里在半径上 -1 是为了不让图片超出边框）
            canvas.drawCircle(width / 2, height / 2, width / 2 - 1, paint);
        } else if (shapeType == 2) {
            // 当ShapeType == 2 时 图片为圆角矩形 （这里在宽高上 -1 是为了不让图片超出边框）
            RectF rectf = new RectF(1, 1, getWidth() - 1, getHeight() - 1);
            canvas.drawRoundRect(rectf, radius + 1, radius + 1, paint);
        }

        paint.setXfermode(xfermode);

        // 空间的大小 / bitmap 的大小 = bitmap 缩放的倍数
        float scaleWidth = ((float) getWidth()) / bitmap.getWidth();
        float scaleHeight = ((float) getHeight()) / bitmap.getHeight();

        Matrix matrix = new Matrix();
        matrix.postScale(scaleWidth, scaleHeight);

        //bitmap 缩放
        bitmap = Bitmap.createBitmap(bitmap, 0, 0, bitmap.getWidth(), bitmap.getHeight(), matrix, true);

        //draw 上去
        canvas.drawBitmap(bitmap, 0, 0, paint);
        canvas.restore();
    }

    /**
     * 绘制控件的按下效果
     *
     * @param canvas
     */
    private void drawPress(Canvas canvas) {
        // 这里根据类型判断绘制的效果是圆形还是矩形
        if (shapeType == 1) {
            // 当ShapeType == 1 时 图片为圆形 （这里在半径上 -1 是为了不让图片超出边框）
            canvas.drawCircle(width / 2, height / 2, width / 2 - 1, pressPaint);
        } else if (shapeType == 2) {
            // 当ShapeType == 2 时 图片为圆角矩形 （这里在宽高上 -1 是为了不让图片超出边框）
            RectF rectF = new RectF(1, 1, width - 1, height - 1);
            canvas.drawRoundRect(rectF, radius + 1, radius + 1, pressPaint);
        }
    }

    /**
     * 绘制自定义控件边框
     *
     * @param canvas
     */
    private void drawBorder(Canvas canvas) {
        if (borderWidth > 0) {
            Paint paint = new Paint();
            paint.setStrokeWidth(borderWidth);
            paint.setStyle(Paint.Style.STROKE);
            paint.setColor(borderColor);
            paint.setAntiAlias(true);
            // 根据控件类型的属性去绘制圆形或者矩形
            if (shapeType == 1) {
                canvas.drawCircle(width / 2, height / 2, (width - borderWidth) / 2, paint);
            } else if (shapeType == 2) {
                // 当ShapeType = 1 时 图片为圆角矩形
                RectF rectf = new RectF(borderWidth / 2, borderWidth / 2, getWidth() - borderWidth / 2,
                        getHeight() - borderWidth / 2);
                canvas.drawRoundRect(rectf, radius, radius, paint);
            }
        }
    }

    /**
     * 重写父类的 onSizeChanged 方法，检测控件宽高的变化
     *
     * @param w
     * @param h
     * @param oldw
     * @param oldh
     */
    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(w, h, oldw, oldh);
        width = w;
        height = h;
    }

    /**
     * 重写 onTouchEvent 监听方法，用来监听自定义控件是否被触摸
     *
     * @param event
     * @return
     */
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                pressPaint.setAlpha(pressAlpha);
                invalidate();
                break;
            case MotionEvent.ACTION_UP:
                pressPaint.setAlpha(0);
                invalidate();
                break;
            case MotionEvent.ACTION_MOVE:

                break;
            default:
                pressPaint.setAlpha(0);
                invalidate();
                break;
        }
        return super.onTouchEvent(event);
    }

    /**
     * 这里是参考其他开发者获取Bitmap内容的方法， 之前是因为没有考虑到 Glide 加载的图片
     * 导致drawable 类型是属于 SquaringDrawable 类型，导致强转失败
     * 这里是通过drawable不同的类型来进行获取Bitmap
     *
     * @param drawable
     * @return
     */
    private Bitmap getBitmapFromDrawable(Drawable drawable) {
        try {
            Bitmap bitmap;
            if (drawable instanceof BitmapDrawable) {
                return ((BitmapDrawable) drawable).getBitmap();
            } else if (drawable instanceof ColorDrawable) {
                bitmap = Bitmap.createBitmap(COLORDRAWABLE_DIMENSION, COLORDRAWABLE_DIMENSION, BITMAP_CONFIG);
            } else {
                bitmap = Bitmap.createBitmap(drawable.getIntrinsicWidth(), drawable.getIntrinsicHeight(),
                        BITMAP_CONFIG);
            }
            Canvas canvas = new Canvas(bitmap);
            drawable.setBounds(0, 0, canvas.getWidth(), canvas.getHeight());
            drawable.draw(canvas);
            return bitmap;
        } catch (OutOfMemoryError e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 设置边框颜色
     *
     * @param borderColor
     */
    public void setBorderColor(int borderColor) {
        this.borderColor = borderColor;
        invalidate();
    }

    /**
     * 设置边框宽度
     *
     * @param borderWidth
     */
    public void setBorderWidth(int borderWidth) {
        this.borderWidth = borderWidth;
    }

    /**
     * 设置图片按下颜色透明度
     *
     * @param pressAlpha
     */
    public void setPressAlpha(int pressAlpha) {
        this.pressAlpha = pressAlpha;
    }

    /**
     * 设置图片按下的颜色
     *
     * @param pressColor
     */
    public void setPressColor(int pressColor) {
        this.pressColor = pressColor;
    }

    /**
     * 设置倒角半径
     *
     * @param radius
     */
    public void setRadius(int radius) {
        this.radius = radius;
        invalidate();
    }

    /**
     * 设置形状类型
     *
     * @param shapeType
     */
    public void setShapeType(int shapeType) {
        this.shapeType = shapeType;
        invalidate();
    }
}
```

### 控件使用方法
使用方法很简单，就像在`xml`布局文件使用其他控件你那样引用就好；如果你不想引用过多的库， 可以直接复制`MLImageView`类到自己的项目中，
进行修改加工，让控件和自己的项目进行整合
```xml
<net.melove.dome.mlimageview.MLImageView
        android:layout_width="96dp"
        android:layout_height="96dp"
        android:layout_margin="8dp"
        android:src="@mipmap/lz_bp_blue"
        melove:border_color="@color/ml_white"
        melove:border_width="4dp"
        melove:press_alpha="50"
        melove:press_color="#00ff00"
        melove:radius="8dp"
        melove:shape_type="rectangle" />
```
### 注意
要记着有一点，在使用自定义控件之前一定要添加自定义命名空间（命名空间的名字可以自己定义）
```xml
xmlns:melove="http://schemas.android.com/apk/res-auto"
```
### 项目源码
[自定义ImageView控件 MLImageViewDemo](https://github.com/lzan13/MLImageViewDemo "自定义ImageView控件 MLImageViewDemo")