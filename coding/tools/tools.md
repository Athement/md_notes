#### 判断矩形是否重叠

矩形的不同描述形式,使用不同的方法判断重叠

**两点式**

```java
public boolean isOverlap(int[] rec0,int[] rec1){
	return !(rec0[0]>=rec1[2]||rec0[2]<=rec1[0]||rec0[1]>=rec1[3]||rec0[3]<=rec1[1]);
}
```

rec0和rec1位长度为4的数组,4个值分别表示矩形的左下点(x1,y1)和右上点(x2,y2).

`rec0[0]>=rec1[2]||rec0[2]<=rec1[0]`表示矩形的竖边不重叠

`rec0[1]>=rec1[3]||rec0[3]<=rec1[1]`表示矩形的横边不重叠

**中心+长宽**

```java
public boolean isOverlap(int[] rec0,int[] rec1){
    return Math.abs(rec0[0]-rec1[0])<(rec0[2]+rec1[2])/2&&Math.abs(rec0[1]-rec1[1])<(rec0[3]+rec1[3])/2
}
```

rec0和rec1位长度为4的数组,4个值分别表示矩形的中点(x,y)和长宽(w,h).