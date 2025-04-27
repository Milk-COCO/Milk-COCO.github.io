
# 缓动类型

与Phigredit中相同

----------------------------------

## 缓动对照表

| 缓动类型序号 | 缓动昵称 | 缓动类型序号 | 缓动昵称 |
| - | ------ | - | ------ |
| 0 | Linear | 8 | CubicOut | 
| 1 | SineIn | 9 | CubicInOut | 
| 2 | SineOut | 10 | QuartIn | 
| 3 | SineInOut | 11 | QuartOut | 
| 4 | QuadIn | 12 | QuartInOut | 
| 5 | QuadOut | 13 | Zero | 
| 6 | QuadInOut | 14 | One | 
| 7 | CubicIn | 其他 | 视为Linear | 

## 代码实现

下面为C#实现

```CS
public static class Easing : Object
{
	public static double EasingCalc(ushort type,double time, double a = 1)
	{
		if(time==0) return 0;
		if(time>1) return 1;
		return type switch
		{
			0 => Linear(time),

			1 => SineIn(time),
			2 => SineOut(time),
			3 => SineInOut(time),

			4 => QuadIn(time),
			5 => QuadOut(time),
			6 => QuadInOut(time),

			7 => CubicIn(time),
			8 => CubicOut(time),
			9 => CubicInOut(time),

			10 => QuartIn(time),
			11 => QuartOut(time),
			12 => QuartInOut(time),
			
			13 => Zero(time),
			15 => One(time),
			
			_ => Linear(time)
		};
	}

	public static double Linear(double k) => k;
	
	public static double SineIn(double k) => 1 - Math.Cos(k * Math.PI / 2);
	public static double SineOut(double k) => Math.Sin(k * Math.PI / 2);
	public static double SineInOut(double k) => 0.5 * (1 - Math.Cos(Math.PI * k));
	
	public static double QuadIn(double k) => k * k;
	public static double QuadOut(double k) => k * (2 - k);
	public static double QuadInOut(double k) => ((k *= 2) < 1) ? 0.5 * k * k : -0.5 * (--k * (k - 2) - 1);

	public static double CubicIn(double k) => k * k * k;
	public static double CubicOut(double k) => --k * k * k + 1;
	public static double CubicInOut(double k) => ((k *= 2) < 1) ? 0.5 * k * k * k : 0.5 * ((k -= 2) * k * k + 2);


	public static double QuartOut(double k) => 1 - (--k * k * k * k);
	public static double QuartIn(double k) => k * k * k * k;
	public static double QuartInOut(double k) => ((k *= 2) < 1) ? 0.5 * k * k * k * k : -0.5 * ((k -= 2) * k * k * k - 2);

	public static double Zero(double k) => 0;
	public static double One(double k) => 1;
}
```