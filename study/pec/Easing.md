
# 缓动类型

----------------------------------

## 缓动对照表

| 缓动类型序号 | 缓动昵称 | 缓动类型序号 | 缓动昵称 |
| - | ------ | - | ------ |
| 1 | Linear | 15 | QuintIn | 
| 2 | SineOut | 16 | ExpoOut | 
| 3 | SineIn | 17 | ExpoIn | 
| 4 | QuadOut | 18 | CircOut | 
| 5 | QuadIn | 19 | CircIn | 
| 6 | SineInOut | 20 | BackOut | 
| 7 | QuadInOut | 21 | BackIn | 
| 8 | CubicOut | 22 | CircInOut | 
| 9 | CubicIn | 23 | BackInOut | 
| 10 | QuartOut | 24 | ElasticOut | 
| 11 | QuartIn | 25 | ElasticIn |
| 12 | CubicInOut | 26 | BounceOut | 
| 13 | QuartInOut | 27 | BounceIn | 
| 14 | QuintOut | 28 | BounceInOut | 
| 其它 | 视为 Linear | 29 | ElasticInOut |

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
			1 => Linear(time),

			2 => SineOut(time),
			3 => SineIn(time),

			4 => QuadOut(time),
			5 => QuadIn(time),

			6 => SineInOut(time),
			7 => QuadInOut(time),

			8 => CubicOut(time),
			9 => CubicIn(time),

			10 => QuartOut(time),
			11 => QuartIn(time),

			12 => CubicInOut(time),
			13 => QuartInOut(time),

			14 => QuintOut(time),
			15 => QuintIn(time),

			16 => ExpoOut(time),
			17 => ExpoIn(time),
			
			18 => CircOut(time),
			19 => CircIn(time),
			
			20 => BackOut(time),
			21 => BackIn(time),
			
			22 => CircInOut(time),
			23 => BackInOut(time),
			
			24 => ElasticOut(time),
			25 => ElasticIn(time),
			
			26 => BounceOut(time),
			27 => BounceIn(time),
			
			28 => BounceInOut(time),
			29 => ElasticInOut(time),
			
			_ => Linear(time)
		};
	}

	public static double Linear(double k) => k;
	
	public static double SineOut(double k) => Math.Sin(k * Math.PI / 2);
	public static double SineIn(double k) => 1 - Math.Cos(k * Math.PI / 2);
	
	public static double QuadOut(double k) => k * (2 - k);
	public static double QuadIn(double k) => k * k;

	public static double SineInOut(double k) => 0.5 * (1 - Math.Cos(Math.PI * k));
	public static double QuadInOut(double k) => ((k *= 2) < 1) ? 0.5 * k * k : -0.5 * (--k * (k - 2) - 1);

	public static double CubicOut(double k) => --k * k * k + 1;
	public static double CubicIn(double k) => k * k * k;


	public static double QuartOut(double k) => 1 - (--k * k * k * k);
	public static double QuartIn(double k) => k * k * k * k;


	public static double CubicInOut(double k) => ((k *= 2) < 1) ? 0.5 * k * k * k : 0.5 * ((k -= 2) * k * k + 2);
	public static double QuartInOut(double k) => ((k *= 2) < 1) ? 0.5 * k * k * k * k : -0.5 * ((k -= 2) * k * k * k - 2);

	public static double QuintOut(double k) => --k * k * k * k * k + 1;
	public static double QuintIn(double k) => k * k * k * k * k;

	public static double ExpoOut(double k) => k == 1 ? 1 : 1 - Math.Pow(2, -10 * k);
	public static double ExpoIn(double k) => k == 0 ? 0 : Math.Pow(1024, k - 1);

	public static double CircOut(double k) => Math.Sqrt(1 - (--k * k));
	public static double CircIn(double k) => 1 - Math.Sqrt(1 - k * k);


	public static double BackOut(double k)
	{
		double s = 1.70158;
		return --k * k * ((s + 1) * k + s) + 1;
	}

	public static double BackIn(double k)
	{
		double s = 1.70158;
		return k * k * ((s + 1) * k - s);
	}
	

	public static double CircInOut(double k)
	{
		if ((k *= 2) < 1)
			return -0.5 * (Math.Sqrt(1 - k * k) - 1);
		return 0.5 * (Math.Sqrt(1 - (k -= 2) * k) + 1);
	}

	public static double BackInOut(double k)
	{
		double s = 1.70158 * 1.525;

		if ((k *= 2) < 1)
			return 0.5 * (k * k * ((s + 1) * k - s));
		
		return 0.5 * ((k -= 2) * k * ((s + 1) * k + s) + 2);
	}


	public static double ElasticOut(double k)
	{
		if (k == 0) return 0;
		if (k == 1) return 1;

		double a;
		double s;
		double p = 0.4;


        a = 1;
        s = p / 4; //0.1

		return a * Math.Pow(2, -10 * k) * Math.Sin((k - s) * (2 * Math.PI) / p) + 1;
	}

	public static double ElasticIn(double k)
	{
		if (k == 0) return 0;
		if (k == 1) return 1;

		double a;
		double s;
		double p = 0.4;

		a = 1;
		s = p / 4;

		return -a * Math.Pow(2, 10 * (k -= 1)) * Math.Sin((k - s) * (2 * Math.PI) / p);
	}


	public static double BounceOut(double k)
	{
		if (k < (1 / 2.75))
			return 7.5625 * k * k;
		else if (k < (2 / 2.75))
			return 7.5625 * (k -= (1.5 / 2.75)) * k + 0.75;
		else if (k < (2.5 / 2.75))
			return 7.5625 * (k -= (2.25 / 2.75)) * k + 0.9375;
		else
			return 7.5625 * (k -= (2.625 / 2.75)) * k + 0.984375;
	}
	public static double BounceIn(double k) => 1 - BounceOut(1 - k);


	public static double ElasticInOut(double k)
	{
		if (k == 0) return 0;
		if (k == 1) return 1;

		double a
		double s;
		double p = 0.4;

		a = 1;
		s = p / 4;

		if ((k *= 2) < 1)
			return -0.5 * (a * Math.Pow(2, 10 * (k -= 1)) * Math.Sin((k - s) * (2 * Math.PI) / p));
		
		return a * Math.Pow(2, -10 * (k -= 1)) * Math.Sin((k - s) * (2 * Math.PI) / p) * 0.5 + 1;
	}

	public static double BounceInOut(double k) => (k < 0.5) ?
        BounceIn(k * 2) * 0.5 : BounceOut(k * 2 - 1) * 0.5 + 0.5;
}
```