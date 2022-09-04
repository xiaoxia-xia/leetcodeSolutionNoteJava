# 25 Reverse_Nodes_in_k-Group

## **Analysis**:

for n elements, for each k elements, we reverse them, for example:
> [1,2,3,4,5,6,7,8]

with k = 3, will turn out to be
> [3,2,1, 6,5,4, 7,8]

,so as we reverse [1,2,3] and [4,5,6], and remain [7,8]


The first thought will be we take out each k-length, reverse them, then reconnect with each k-length part
But we do have to consider for the last part that length < k, we don't do anything with it

 ---
 ## **First Try**

 ```
 class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if(k == 1){
            return head;
        }
        List<ListNode> tailList = new ArrayList<>();
        List<ListNode> headList = new ArrayList<>();
        int cnt = 1;
        while(head != null){
            if(cnt == 1){
                tailList.add(head);
            }
            if(cnt == k){
                headList.add(head);
                cnt = 0;
            }
            cnt++;
            head = head.next;
        }
        ListNode remain = null;
        if(tailList.size() > headList.size()){
            remain = tailList.get(tailList.size() - 1);
            tailList.remove(remain);
        }
        for(int i = 0; i < tailList.size(); i++){
            ListNode curr = tailList.get(i);
            ListNode next = curr.next;
            ListNode nextNext = next.next;
            while(curr != headList.get(i)){
                next.next = curr;
                curr = next;
                next = nextNext;
                if(nextNext != null){
                    nextNext = nextNext.next;
                }
            }
        }
        for(int i = 0; i < tailList.size() - 1; i++){
            tailList.get(i).next = headList.get(i + 1);
        }
        tailList.get(tailList.size() - 1).next = remain;
        
        return headList.get(0);
    }
}
```

## Explain:

We constuct two Lists, to store the heads and tails of each part, as
```
List<ListNode> tailList = new ArrayList<>();
List<ListNode> headList = new ArrayList<>();
```

So let given to be store as first tail (since we reverse heads and tails), as `head.next` not to be null, we have 
`(1)`st, `(1+k)`th, `(1+2k)`th ... nodes in `tailList`, and
`(k)`st, `(2k)`th, `(3k)`th ... nodes in `headList`

If we have 1 more tail in the `tailList` (which supposed to be one more head in original NodeList), implied that we have one part not reverse as `remain`, so

```
if(tailList.size() > headList.size()){
    remain = tailList.get(tailList.size() - 1);
    tailList.remove(remain);
}
```

otherwise `remain` will be `null`, by either case to connect at the end of answer


for each part with head and tail respectively, we reverse them with 
```
for(int i = 0; i < tailList.size(); i++){
        ListNode curr = tailList.get(i);
        ListNode next = curr.next;
        ListNode nextNext = next.next;
        while(curr != headList.get(i)){
            next.next = curr;
            curr = next;
            next = nextNext;
            if(nextNext != null){
                nextNext = nextNext.next;
            }
        }
}
```

Notice that to reverse the direction, we always need 3 nodes holding on the same time, usually as `prev`, `curr`,and `next` or in this case, we use `curr`, `next`, and `nextNext`.

For each time in while loop, we move forward with 3 of them, until curr reach the head in the `headList` of the same index as `tailList`

But for this time, we should be aware of that if `k == 1`, as the curr reach the last one, the `next` will be `null`, and `nextNext` will be `outOfBound` error, since that, and we also know case `k == 1` is easiest one to deal with, we have `if(k == 1){return head;}` at the beginning.

For `k > 1` case, as we reach to the last one in the `tailList`, and if without `remain`, we still take risk of being out of bound for nextNext, so we need to check `nextNext != null` for each loop

In the end, we reconnect each part by
```
for(int i = 0; i < tailList.size() - 1; i++){
    tailList.get(i).next = headList.get(i + 1);
}
```
and connect remain as
```
tailList.get(tailList.size() - 1).next = remain;
```


## optimization thought
To reverse each part can be taken out to be a function to increase readability. To store node in the headList and tailList is using O(n) memeory. To cutting whole list into part using recursive can same many work

---

## **Final Solution**
```
class Solution {
    public void reverse(ListNode st, ListNode end)
    {
      ListNode curr = st;
      ListNode prev = null;
      ListNode next = null;
      while(prev != end)
      {
        next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
      }
    }
    public ListNode reverseKGroup(ListNode head, int k) 
    {
      if(head == null || k == 1)
      {
        return head;
      }
      ListNode st = head;
      ListNode end = head;
      int inc = k-1;
      while(inc--!=0)
      {
        end = end.next;
        if(end == null)
        {
          return head;
        }
      }
      ListNode node = reverseKGroup(end.next, k);
      reverse(st, end);
      st.next = node;
      
        return end;
    }
}
```

## Explain:
if `length` of node list `n` has `n / k == 0`, we have
```
while(inc--!=0){
    end = end.next;
    if(end == null)
    {
        return head;
    }
    }
```
to return the original

If n / k > 0, we use recursive as 
```
ListNode node = reverseKGroup(end.next, k);
reverse(st, end);
st.next = node;
```

To cut the first k nodes off the n, then redo the test as `(n - k) / k ?= 0` , and keep doing that until `n / k == 0`

and of course we have 
```
if(head == null || k == 1)
{
    return head;
}
```
to take care of case if `n == 0` or `n == k`

As we have `n / k == 0`, we keep it as it is, as remain 


And reverse the part is given by
```
public void reverse(ListNode st, ListNode end){
    ListNode curr = st;
    ListNode prev = null;
    ListNode next = null;
    while(prev != end)
    {
    next = curr.next;
    curr.next = prev;
    prev = curr;
    curr = next;
    }
}
```
 `remain` or `null` as `remain`. We reconnect by `st.next = node;`

