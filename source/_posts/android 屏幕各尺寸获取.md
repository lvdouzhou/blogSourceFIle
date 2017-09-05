---
title: android 屏幕各尺寸获取
date: 2017-9-5 17:18:29
categories: “android” ,“android 屏幕尺寸”
tags:
    - android
description: "android 屏幕各尺寸获取"
---

在开发中我们会遇到各种需要获得屏幕参数的场景，当中也有不少坑，所以现在就记录一下这些参数的获取方式。以免再入坑。

## 物理屏幕宽高
一、底部没有虚拟按键
   这里获取到的宽高，就是你眼睛能看到的，屏幕亮着的地方的宽高。
```

	/**
	 * 获取屏幕的宽
	 *
	 * @param context
	 * @return
	 */
	public static int getScreenWidth(Context context) {
		WindowManager wm = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);
		DisplayMetrics dm = new DisplayMetrics();
		wm.getDefaultDisplay().getMetrics(dm);
		return dm.widthPixels;
	}

	/**
	 * 获取屏幕的高度
	 *
	 * @param context
	 * @return
	 */
	public static int getScreenHeight(Context context) {
		WindowManager wm = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);
		DisplayMetrics dm = new DisplayMetrics();
		wm.getDefaultDisplay().getMetrics(dm);
		return dm.heightPixels;
	}
	

```

<!-- more -->
二、底部有虚拟按键
华为手机底部都会有一个黑色的虚拟按键(NavigationBar)，通过上面这个方式得到的屏幕高度是屏幕**真是高度-虚拟按键的高度**。所以有虚拟按键的情况获取屏幕的高度就是另一种方法了。
```
	public static int getRealHeight(Context context) {
		WindowManager wm = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);
		Display display = wm.getDefaultDisplay();
		int screenHeight = 0;

		if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN_MR1) {
			DisplayMetrics dm = new DisplayMetrics();
			display.getRealMetrics(dm);
			screenHeight = dm.heightPixels;

			//或者也可以使用getRealSize方法
//            Point size = new Point();
//            display.getRealSize(size);
//            screenHeight = size.y;
		} else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.ICE_CREAM_SANDWICH) {
			try {
				screenHeight = (Integer) Display.class.getMethod("getRawHeight").invoke(display);
			} catch (Exception e) {
				DisplayMetrics dm = new DisplayMetrics();
				display.getMetrics(dm);
				screenHeight = dm.heightPixels;
			}
		}
		return screenHeight;
	}

```




## 虚拟按键高度
虚拟按键(NavigationBar)高度可以通过读取定义在Android系统尺寸资源中的 navigation_bar_height 获得。
所以不管虚拟按键是显示还是隐藏，得到的结果都是一样的。
```
	public static int getNavigationBarHeight(Context context) {
		int navigationBarHeight = -1;
		Resources resources = context.getResources();
		int resourceId = resources.getIdentifier("navigation_bar_height","dimen", "android");
		if (resourceId > 0) {
			navigationBarHeight = resources.getDimensionPixelSize(resourceId);
		}
		return navigationBarHeight;
	}

```

## 状态栏高度 
状态栏就是屏幕顶部显示时间，电池，wifi 等信息的栏目。
1. 方法一：系统提供了一个Resource类，通过这个类可以获取资源文件，借此可以获取 到status_bar_height 。
```

	public int getStatusBarHeight() {
		int result = 0;
		int resourceId = getResources().getIdentifier("status_bar_height", "dimen", "android");
		if (resourceId > 0) {
			result = getResources().getDimensionPixelSize(resourceId);
		}
		return result;
	}
	
```

2. 方法2： 通过反射
Android的所有资源都会有惟一标识在R类中作为引用。我们也可以通过反射获取R类的实例域，然后找 status_bar_height。
```
	public void getStatusBarHeightByReflect() {
		int statusBarHeight2 = -1;
		try {
			Class<?> clazz = Class.forName("com.android.internal.R$dimen");
			Object object = clazz.newInstance();
			int height = Integer.parseInt(clazz.getField("status_bar_height")
					.get(object).toString());
			statusBarHeight2 = getResources().getDimensionPixelSize(height);
		} catch (Exception e) {
			e.printStackTrace();
		}
		Log.e(TAG, "状态栏高度-反射方式：" + statusBarHeight2);
	}
	

```
3. 借助应用区 top 属性。
状态栏位于屏幕的最顶端，坐标从 (0,0) 开始，所以应用区的顶部的位置就是状态栏的高度。 
```


	/**
	 * 应用区的顶端位置即状态栏的高度
	 * *注意*该方法不能在初始化的时候用
	 * */
	public void getStatusBarHeightByTop() {
		
		Rect rectangle = new Rect();
		getWindow().getDecorView().getWindowVisibleDisplayFrame(rectangle);
		Log.e(TAG, "状态栏高度-应用区顶部:" + rectangle.top);
	}
	
```

## 应用区域高度  
除去状态栏剩下的都时应用区。由此可知**屏幕的高度 - 状态栏高度 = 应用区的高度**。

```
/**
	 * 不能在 onCreate 方法中使用。
	 * 因为这种方法依赖于WMS（窗口管理服务的回调）。正是因为窗口回调机制，所以在Activity初始化时执行此方法得到的高度是0。
	 * 这个方法推荐在回调方法onWindowFocusChanged()中执行，才能得到预期结果。
	 */
	public void getAppViewHeight(){
		//屏幕
		DisplayMetrics dm = new DisplayMetrics();
		getWindowManager().getDefaultDisplay().getMetrics(dm);
		//应用区域
		Rect outRect1 = new Rect();
		getWindow().getDecorView().getWindowVisibleDisplayFrame(outRect1);
		int statusBar = dm.heightPixels - outRect1.height();  //状态栏高度=屏幕高度-应用区域高度
		Log.e(TAG, "应用区高度:" + statusBar);
	}

```

## setContentView 高度，view 显示的高度
需要在见面创建后才能获取到。

```
public static int getContentViewHeight(Activity activity) {
		Rect rectangle= new Rect();
		activity.getWindow().findViewById(Window.ID_ANDROID_CONTENT).getDrawingRect(rectangle);
		return rectangle.height();
	}
	
```

## 标题栏高度
标题栏高度 = 应用区高度 - view 显示高度
```
    public static void getTitleBarHeight(Activity activity) {
		Rect outRect1 = new Rect();
		activity.getWindow().getDecorView().getWindowVisibleDisplayFrame(outRect1);

		int viewTop = activity.getWindow().findViewById(Window.ID_ANDROID_CONTENT).getTop();   //要用这种方法
		int titleBarH = viewTop - outRect1.top;

		Log.e(TAG, "标题栏高度-计算:" + titleBarH);
	}

```