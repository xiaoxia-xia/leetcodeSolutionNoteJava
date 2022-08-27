# 42 Trapping_Rain_Water    

## **Analysis**:

Aa example showed as:
>example = [0,1,0,2,1,0,1,3,2,1,2,1]

After we pouring down the water, it became:

>pouring = [0,1,1,2,3,3,3,3,2,2,2,1]

Ideally, for each i, we sum the pouring[i] - example[i] get the answer out

How we achieve pouring? We observing that:
 - pouring will always be have middle high, sides low, like a mountain, which implied that before we reach peak, we keep going up, after last peak, we keep going down. if existing mutiple peak, we go flat

 ---
 ## **First Try**

 ```
class Solution {
    public int trap(int[] height) {
        int ans = 0;
        List<Integer> maxIndex = new ArrayList<>();
        int max = 0;
        for(int i = 0; i < height.length; i++){
            if(max < height[i]){
               max = height[i]; 
            }
        }
        for(int i = 0; i < height.length; i++){
            if(max == height[i]){
               maxIndex.add(i);
            }
        }
        int localMax = height[0];
        for(int i = 1; i < maxIndex.get(0); i++){
            if(localMax > height[i]){
                ans += (localMax - height[i]);
            }else if(localMax < height[i]){
                localMax = height[i];
            }
        }
        if(maxIndex.size() >= 2){
            for(int i = maxIndex.get(0); i < maxIndex.get(maxIndex.size() - 1); i++){
                {
                    ans += (max - height[i]);
                }               
            }
        }
        localMax = height[height.length - 1];
        for(int i = height.length - 1 ;i >= maxIndex.get(maxIndex.size() - 1); i--){
            if(localMax > height[i]){
                ans += (localMax - height[i]);
            }else if(localMax < height[i]){
                localMax = height[i];
            }
        }
        
        return ans;
        
        
    }
}
```

## Explain:

first time in loop:
```
for(int i = 0; i < height.length; i++){
            if(max < height[i]){
               max = height[i]; 
            }
        }
```
we gain the peak val, storing in max

second time in loop:
```
List<Integer> maxIndex = new ArrayList<>();
for(int i = 0; i < height.length; i++){
            if(max == height[i]){
               maxIndex.add(i);
            }
        }
```
we store all peak value in list maxIndex

then in the third loop, we cut it into 3 part, including
1. From start to first peak
2. Frome first peak to last peak
3. from last peak to end

part 1 and 3 are typically the same, except with reverse order
Take part 1 as example:
```
int localMax = height[0];
        for(int i = 1; i < maxIndex.get(0); i++){
            if(localMax > height[i]){
                ans += (localMax - height[i]);
            }else if(localMax < height[i]){
                localMax = height[i];
            }
        }
```

since in the part 1 of pouring, right is always be higher or equal. We use localMax to keep the maximum of left side of current. so in pouring, in this index i, will have value `localMax`, and the value, in this index i, before pouring, is `height[i]`, then add difference, `localMax - height[i]`, to the answer

if we encounter the value larger than `localMax`, we know `localMax` is no longer be real local max, we update it, and no pouring on this index


For the part 2, since we may have one peak, so part 2 may not exist, we have
```
if(maxIndex.size() >= 2){
    }
```



## optimization thought

we use 3 loops for this solution, but acually we can pretty confirm increcement of some part of pouring without going over the whole process, which means we can do recording peak value, find peak index, and pouring in the same loop, to reduced the process time.



---

## **Final Solution**

```
class Solution {
    public int trap(int[] height) {
        int left = 0, right = height.length-1;
        int minh = 0;
        int sum = 0;
        while (left < right) {
            if (height[left] <= height[right]) {
                if (minh > height[left]) {
                    sum += minh - height[left];
                } else {
                    minh = height[left];
                }
                ++left;
            } else {
                if (minh > height[right]) {
                    sum += minh - height[right];
                } else {
                    minh = height[right];
                }
                --right;
            }
        }
        return sum;
    }
}
```
## Explain:

For the sides of mountain, we can just record the localMax of both sides as `minh` by just one variable. Then we climb this mountain from both side, to pouring in the same time.

one index from left, as `left`, another index form right, as `right`, once they get meet, we stop.

one first to compare value of these two, as `height[left]` and `height[right]`, and to deal with lower one. For the lower value one, we assume it is `height[left]`, we can know that left is definitely not in the part 3, then we compare it with `minh`, 
- case 1: `height[left] > minh`, then we have height[right] > height[left] > minh, as localMax, minh is too low so need to be updated at least with `minh = height[left]`. Then move on left with `left++`

- case 2: `height[left] < minh`, then we know minh is produced by left, as `minh = height[left - someIndex]` somewhere, implied as `minh = max(leftSides)`. Otherwise if max(rightSides) = minh > max(leftSides), the index of minh in right will not move as right--. So we have `minh - height[left]` to add on, then move on with `left++`

- case 3: height[left] == minh, then nothing to do but move on with `left++`


Yes there are `height[left] == height[right]` needed to be dicussion. Since we know there at least one, we call it as right by assume without losing genearity, has height[right] >= minh. So height[left] == height[right] >= minh, then we choose as case 2 or 3, then move on with `left++`, now we may in pouring part 1 or part 2, but if think of peak as one way of localMax, they are the same way to process.


