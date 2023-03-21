# 算法刷题总结

## HOT100-11 盛最多水的容器

### 题目内容

给定一个长度为 n 的整数数组 height 。有 n 条垂线，第 i 条线的两个端点是 (i, 0) 和 (i, height[i]) 。

找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

示例1：  

![Alt](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

示例2：

```
输入：height = [1,1]
输出：1
```

### 题解思路

算法核心思想：**缩减搜索空间**。  
涉及方法：**双指针法**

### 题解代码

```java
class Solution {
    public int maxArea(int[] height) {
        // 定义双指针
        int i = 0;
        int j = height.length - 1;
        int res = 0;
        while(i<j) {
            int area = (j-i) * Math.min(height[i], height[j]);
            res = Math.max(res, area);
            // 当左指针高度小于右指针高度时，说明此时左指针的高度已经不满足最优解，左指针加一
            if(height[i] < height[j]) {
                i++;
            } else {
                j--;
            }
        }
        return res;
    }
}
```

## HOT100-15 三数之和

### 题目内容

给你一个整数数组 nums ，判断是否存在三元组 [nums[i], nums[j], nums[k]] 满足 i != j、i != k 且 j != k ，同时还满足 nums[i] + nums[j] + nums[k] == 0 。请

你返回所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。

示例1：

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。
```

示例2：

```
输入：nums = [0,1,1]
输出：[]
解释：唯一可能的三元组和不为 0 。
```

示例3：

```
输入：nums = [0,0,0]
输出：[[0,0,0]]
解释：唯一可能的三元组和为 0 。
```

### 题解思路

核心思想：**排序 + 双指针**  

### 题解代码

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        // 排除特殊情况
        if (nums == null || nums.length < 3) {return ans;}
        // 数组排序
        Arrays.sort(nums);
        int len = nums.length;
        for (int i=0;i<len;i++) {
            // 如果排序后第一个数大于0 ，后续的数之和不可能为0，跳出循环
            if (nums[i] > 0) break;
            // 去除重复解
            if (i > 0 &&nums[i] == nums[i-1]) continue;
            int left = i + 1;
            int right = len - 1;
            while(left<right) {
                int sum = nums[i] + nums[left] + nums[right];
                if(sum==0) {
                    ans.add(Arrays.asList(nums[i], nums[left], nums[right]));
                    // 去除重复解
                    while(left<right &&nums[left]== nums[left+1]) left++;
                    //  去除重复解
                    while(left<right&&nums[right]==nums[right-1]) right--;
                    left++;
                    right--;
                }else if(sum>0) {
                    right--;
                }else {
                    left++;
                }
            }
        }
        return ans;
    }
}
```

## HOT100-17 电话号码的字母组合

### 题目内容

给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。答案可以按 任意顺序 返回。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![Alt](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2021/11/09/200px-telephone-keypad2svg.png)

示例1：

```console
输入：digits = "23"
输出：["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

示例2:

```console
输入：digits = ""
输出：[]
```

示例3：

```console
输入：digits = "2"
输出：["a","b","c"]
```

### 题解思路

- 使用哈希表存储每个数字对应的所有可能的字母，然后进行回溯操作
- 回溯过程中维护一个字符串，表示已有的字母排列（如果未遍历完电话号码的所有数字，则已有的字母排列是不完整的）。该字符串初始为空。每次取电话号码的一位数字，从哈希表中获得该数字对应的所有可能的字母，并将其中的一个字母插入到已有的字母排列后面，然后继续处理电话号码的后一位数字，直到处理完电话号码中的所有数字，即得到一个完整的字母排列。然后进行回退操作，遍历其余的字母排列。
- 由于每个数字对应的每个字母都可能进入字母组合,直接穷举所有的解

### 题解代码

```java
class Solution {
    public List<String> letterCombinations(String digits) {

        List<String> ans = new ArrayList<>();
        if (digits.length() == 0) return ans;
        // 定义数字——字母映射
        Map<Character, String> phoneMap = new HashMap<> () {{
            put('2', "abc");
            put('3', "def");
            put('4', "ghi");
            put('5', "jkl");
            put('6', "mno");
            put('7', "pqrs");
            put('8', "tuv");
            put('9', "wxyz");
        }};

        backtrack(ans, phoneMap, digits, 0, new StringBuffer());
        return ans;
    }

    public void backtrack(List<String> combinations, Map<Character, String> phoneMap, String digits, int index, StringBuffer combination) {
        if (index == digits.length()) {
            combinations.add(combination.toString());
        } else {
            // 根据index 获取到当前数字对应的 字母字符串[“abc”]，然后遍历字符串，对每个字符再次进入递归过程，当递归到最底层开始组合
            Character digit = digits.charAt(index);
            String letters = phoneMap.get(digit);
            int letterLen = letters.length();
            for (int i = 0; i < letterLen;i++) {
                combination.append(letters.charAt(i));
                backtrack(combinations, phoneMap, digits, index + 1, combination);
                combination.deleteCharAt(index);
            }
        }
    }

}
```

## HOT100-19 删除链表的倒数第 N 个结点

### 题目内容

给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

示例1：  
![Alt](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)  

```console
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

示例2：

```console
输入：head = [1], n = 1
输出：[]
```

示例3：

```console
输入：head = [1,2], n = 1
输出：[1]
```

### 题解思路

- 方法1：回溯法  
  - 特殊情况判断：当输入为空时，直接返回空
  - 进入递归，结束条件为```head.next == null```，返回1，作为当前结点的编号。判断当前结点编号是否为(n+1)，即需要删除结点的上一个结点，然后删除倒数第n个结点
  - 在最外层判断n值是否大于等于链表长度，当等于链表长度时说明需要处理头结点，删除头结点，即使用```return head.next```返回头结点的下一个结点。
- 方法2：双指针  
  - 新建一个结点dummy指向当前链表head，便于后续处理头结点
  - 新建first指针指向head，另一个指针second指向dummy。移动first位置，共移动n次，作为固定长度n。
  - 当first不为null时，继续移动first指针，当first为空时，说明此时first指向了链接结尾null位置。此时second指向了需要删除结点的上一个结点，通过```second.next = second.next.next```即删除倒数第n个结点。

### 题解代码

- 回溯法

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {

        ListNode head1 = head;
        // 当链表为空时返回空
        if (head== null) return null;
        int length = backTrack(head, n);
        // 如果n大于等于链表长度，则删除头结点，返回下一个结点
        return (n >= length) ? (head.next) : head;

    }

    public Integer backTrack(ListNode head, int n) {
        if (head.next == null) {
            return 1;
        } else {
            int index = backTrack(head.next, n) + 1;
            if (index == n + 1) {
                // 当n=1的时候，直接将head.next置空
                head.next = (n==1)?null:head.next.next;
            }
            return index;
        }
    }
}
```

- 双指针

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {

        ListNode newHead = new ListNode(0, head);
        ListNode first = head;
        ListNode second = newHead;

        for (int i = 0; i < n ; i++) {
            first = first.next;
        }

        while (first != null) {
            first = first.next;
            second = second.next;
        }

        second.next = second.next.next;
        return newHead.next;
    }
}
```

## HOT100-20 有效的括号

### 题目内容

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
每个右括号都有一个对应的相同类型的左括号。  
示例1：

```console
输入：s = "()"
输出：true
```  

示例2：

```console
输入：s = "()[]{}"
输出：true
```  

示例3：

```console
输入：s = "(]"
输出：false
```

### 题解思路

核心思想：**栈**  

- 当遇到左括号时，将左括号压入栈中
- 当遇到右括号时，判断此时栈是否为空，空的话说明匹配错误，返回false；否则从栈中pop出字符进行比对
- 遍历完后判断栈是否为空，空的话说明匹配正确， 返回true；否则返回false

![Alt](https://pic.leetcode-cn.com/baa8829ac398e665eb645dca29eadd631e2b337e05022aa5a678e091471a4913-20.gif)

### 题解代码

```java
class Solution {
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();

        for (int i = 0;i<s.length();i++) {
            char c = s.charAt(i);
            // 左括号时压入栈中
            if (c == '{' || c == '[' || c == '(') {
                stack.push(c);
            }else if (c == '}' || c == ')' || c == ']') {
                // 当栈不为空且遇到右边括号时，从栈顶获取获取值进行判断
                if (!stack.empty()) {
                    char tmpC = stack.pop();
                    if (c == ')' && tmpC!='(') return false;
                    else if (c == '}' && tmpC!='{') return false;
                    else if(c == ']' && tmpC!='[') return false;
                } else {
                    // 栈为空，但此时遇到右括号，说明匹配出错，返回false
                    return false;
                }
            }else {
                // 遇到不明符号，返回false
                return false;
            }
        }
        // 当栈最后为空时，说明能够全部匹配，返回true
        if (stack.empty()) {
            return true;
        }else {
            return false;
        }
    }
}
```

## HOT100-21 合并两个有序链表

### 题目内容

将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。  
示例1：
![Alt](https://assets.leetcode.com/uploads/2020/10/03/merge_ex1.jpg)
```console
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```
示例2：
```console
输入：l1 = [], l2 = []
输出：[]
```
示例3：
```console
输入：l1 = [], l2 = [0]
输出：[0]
```

### 题解思路

- 暴力解法  
新建一个链表，用于存储合并后的内容。遍历两个链表，从头结点开始比较，判断两个头结点指向的值大小，将更小值的节点链接到新建链表中，知道两个链表都为null。
- 回溯法  
    * 终止条件：当两个链表都为空时，说明合并完成
    * 递归调用：判断l1和l2头结点哪个更小，然后较小结点的next指针指向其余结点的合并结果

### 题解代码

- 暴力解法
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode head = new ListNode();
        ListNode cur = head;

        while (list1 != null || list2 != null) {
            // 两链表都不为空的话，比较两个链表的值
            if (list1!=null&&list2!=null) {
                if(list1.val <= list2.val) {
                    cur.next = list1;
                    list1 = list1.next;
                } else {
                    cur.next = list2;
                    list2 = list2.next;
                }
            // 只有一个链表不为空，直接添加到新链表
            } else if(list1!=null) {
                cur.next = list1;
                list1 = list1.next;
            } else {
                cur.next = list2;
                list2 = list2.next;
            }
            // 新链表指针位置移动
            cur = cur.next;
        }
        return head.next;
    }
}
```
- 回溯法
```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        
        if (list1 == null) {
            return list2;
        }
        if (list2 == null) {
            return list1;
        }

        if (list1.val < list2.val) {
            list1.next = mergeTwoLists(list1.next, list2);
            return list1;
        } else {
            list2.next = mergeTwoLists(list1, list2.next);
            return list2;
        }
    }
}
```

