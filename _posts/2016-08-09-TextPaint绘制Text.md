---
layout: post  
title:  TextPaint绘制Text
categories: [blog ]  
tags: [Tech, ]     
---
# TextPaint绘制Text

****
##FontMetrics简介
     FontMetrics是Paint的内部类，作用是“字体测量”。它里面呢就定义了top,ascent,descent,bottom,leading五个成员变量其他什么没有。
     Baseline是基线，在Android中，文字的绘制都是从Baseline处开始的，Baseline往上至字符“最高处”的距离我们称之为ascent（上坡度），Baseline往下至字符“最低处”的距离我们称之为descent（下坡度）；top的意思其实就是除了Baseline到字符顶端的距离外还应该包含这些符号的高度，bottom的意思也是一样。一般情况下我们极少使用到类似的符号所以往往会忽略掉这些符号的存在，但是Android依然会在绘制文本的时候在文本外层留出一定的边距，这就是为什么top和bottom总会比ascent和descent大一点的原因。而在TextView中我们可以通过xml设置其属性android:includeFontPadding="false"去掉一定的边距值但是不能完全去掉。
##Demon
 ![FontMetrics](http://i2.piimg.com/4851/e9666c5f478233fd.png) 
 ![FontMetrics](http://i2.piimg.com/4851/79ec9808bf6e1f3f.png)
 
    上面两张图就是Demon的运行结果和输出结果，我们分别画出了FontMetrics的top、ascent、baseline、descent和bottom以及这些变量的值。
    因为基线上方为负，所以ascent和top的值都是负数，而且top要大于ascent，原因是要为符号留出位置。因为只有一行文本所以leading恒为0。基线下方为正，所以descent和bottom都是正的，bottom要略大于descent。我们来看一下具体的代码实现：
    Paint.FontMetrics fontMetrics = mTextPaint.getFontMetrics();
        textWidth = mTextPaint.measureText(TEXT);

        canvas.drawText(TEXT, 0, BEGIN + Math.abs(fontMetrics.top), mTextPaint);
        canvas.drawLine(0, BEGIN, baseWidth, BEGIN, mPaint);
        canvas.drawText("top:" + fontMetrics.top, TIP + textWidth, BEGIN, mTipPaint);
        canvas.drawLine(0, BEGIN + Math.abs(fontMetrics.top) - Math.abs(fontMetrics.ascent),
                baseWidth, BEGIN + Math.abs(fontMetrics.top) - Math.abs(fontMetrics.ascent), mPaint);
        canvas.drawText("ascent:" + fontMetrics.ascent, TIP + textWidth, BEGIN + Math.abs(fontMetrics.top) - Math.abs(fontMetrics.ascent),
                mTipPaint);
        canvas.drawLine(0, BEGIN + Math.abs(fontMetrics.top),
                baseWidth, BEGIN + Math.abs(fontMetrics.top), mPaint);
        canvas.drawText("baseline:", TIP + textWidth, BEGIN + Math.abs(fontMetrics.top),
                mTipPaint);
        canvas.drawLine(0, BEGIN + Math.abs(fontMetrics.top) + Math.abs(fontMetrics.descent),
                baseWidth, BEGIN + Math.abs(fontMetrics.top) + Math.abs(fontMetrics.descent), mPaint);
        canvas.drawText("descent:" + fontMetrics.descent, TIP + textWidth, BEGIN + Math.abs(fontMetrics.top)
                + Math.abs(fontMetrics.descent), mTipPaint);
        canvas.drawLine(0, BEGIN + Math.abs(fontMetrics.top) + Math.abs(fontMetrics.bottom),
                baseWidth, BEGIN + Math.abs(fontMetrics.top) + Math.abs(fontMetrics.bottom), mPaint);
        canvas.drawText("bottom:" + fontMetrics.bottom, TIP + textWidth, BEGIN + Math.abs(fontMetrics.top)
                + Math.abs(fontMetrics.bottom) + 15, mTipPaint);

        Log.d("Aige", "ascent：" + fontMetrics.ascent);
        Log.d("Aige", "top：" + fontMetrics.top);
        Log.d("Aige", "leading：" + fontMetrics.leading);
        Log.d("Aige", "descent：" + fontMetrics.descent);
        Log.d("Aige", "bottom：" + fontMetrics.bottom);
        
    首先通过以上代码的验证，我们可以知道Text的绘制是从baseline开始绘制的，所以我们在绘制居中屏幕的Text的时候还可以这样写：
        mTextPaint.setTextSize(50);
        mTextPaint.setColor(Color.BLACK);
        int baseX = (int) (canvas.getWidth() / 2 - mTextPaint.measureText(TEXT) / 2);
        int baseY = (int) ((canvas.getHeight() / 2) - ((mTextPaint.descent() + mTextPaint.ascent()) / 2));
        canvas.drawText(TEXT, baseX, baseY, mTextPaint);
    通过float android.graphics.Paint.descent()获取下坡度值，通过float android.graphics.Paint.ascent()获取上坡度值，然后我们计算出绘制Text的起点坐标（X轴起点坐标＝画布的宽度一半－文字宽度一半，Y轴起点坐标＝画布高度一半－（文字的下坡度＋文字的上坡度）／2），Y轴只是简单的将baseline提高了让Text绘制到了屏幕的中心。