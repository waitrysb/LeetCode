
# 顺序版前65题

### 1.Two Sum(Easy)

* 题意：给定数组和一个数字S，求出数组中两个数，其和为S

* 解法：先将数组所有元素放入一个map，求出数组中每个数被S减掉后的值，如果该值也在map中，说明成功

* ```java
   public int[] twoSum(int[] nums, int target) {
     Map<Integer, Integer> map = new HashMap<>();
     for (int i = 0; i < nums.length; i++) {
       map.put(nums[i], i);
     }
     for (int i = 0; i < nums.length; i++) {
       int sub = target - nums[i];
       if (map.containsKey(sub) && map.get(sub) != i) {
         return new int[]{i, map.get(sub)};
       }
     }
     return null;
   }
   ```

### 2. Add Two Numbers(Medium)

* 题意：给定两个链表，链表按照十进制从低位到高位存数字，求出两个链表的和对应的链表

* 解法：直接用加法的竖式原理，从低位到高位，分别求出进位和余数，并将余数放入结果链表，将进位作为下一次的加数

	> 注意两个链表长度不一的情况  
	
	> 注意最后两个加数进位的情况
	

		Definition for singly-linked list.
		public class ListNode {
		  int val;
		  ListNode next;
		  ListNode(int x) { val = x; }
		}
***

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
  ListNode x1 = l1.next;
  ListNode x2 = l2.next;

  int temp = l1.val + l2.val;
  int carry = temp / 10;
  ListNode res = new ListNode(temp % 10);
  res.next = null;
  ListNode tempNode = res;

  while(x1 != null && x2 != null) {
    temp = x1.val + x2.val + carry;
    carry = temp / 10;

    tempNode.next = new ListNode(temp % 10);
    tempNode = tempNode.next;
    tempNode.next = null;

    x1 = x1.next;
    x2 = x2.next;
  }

  while(x1 != null) {
    temp = x1.val + carry;
    carry = temp / 10;
    tempNode.next = new ListNode((temp) % 10);
    tempNode = tempNode.next;
    tempNode.next = null;
    x1 = x1.next;
  }

  while(x2 != null) {
    temp = x2.val + carry;
    carry = temp / 10;
    tempNode.next = new ListNode((temp) % 10);
    tempNode = tempNode.next;
    tempNode.next = null;
    x2 = x2.next;
  }

  //最后要检查carry!!!
  if(carry != 0) {
    tempNode.next = new ListNode(carry);
    tempNode = tempNode.next;
    tempNode.next = null;
  }

  return res;
}
```

### 3. Longest Substring Without Repeating Characters(Medium)

* 题意：求出字符串未重复字母的最长子串

* 解法：使用滑动窗口求解

	> 如果j不符合，那么将i++  
	
	> 如果j符合，那么将j++，并且更新最大值

	```java
	public int lengthOfLongestSubstring(String s) {
	  int len = s.length();
	  Set<Character> set = new HashSet<>();
	  int max = 0;
	  int i = 0;
	  int j = 0;
	  while(i < len && j < len) {
	    if(!set.contains(s.charAt(j))) {
	      set.add(s.charAt(j++));
	      max = Math.max(max, j - i);
	    } else {
	      set.remove(s.charAt(i++));
	    }
	  }
	  return max;
	}
	```

### 4. Median of Two Sorted Arrays(Hard)

* 题意：求出两个有序数组的中位数

* 思路：原来想的是分别比较两个数组的中位数，根据中位数大小来选择抛弃较小中位数对应数组的下半部分，以及较大中位数数组的上半部分，舍弃的数目一样多为min(m, n)/2。这样做在非负数都很正常，但是**当数组中有负数时就出现了bug**。

* 解法：把这个题目当成数学题目来看的话，显然中位数可以将每个数组分成两个部分，这样总共就有了四个部分。并且两个数组较小部分的最大值要小于较大部分的最小值，而且较小部分总数等于较大部分总数。因此就有了不等式和等式。接下来只需要找到两个划分点即可（两个划分点又有等式关系，所以只要找到一个），使用二分法遍历可能的值，找到成功的值后停止，分奇偶输出。

	          left_part          |        right_part
	    A[0], A[1], ..., A[i-1]  |  A[i], A[i+1], ..., A[m-1]
	    B[0], B[1], ..., B[j-1]  |  B[j], B[j+1], ..., B[n-1]

	> 比较两个部分时，只要保证nums1下半部分最大值不比nums2上半部分最小值大，和nums2下半部分最大值不比nums1上半部分最小值大  
	
	>len(left\_part) = len(right\_part)  
	max(left\_part) ≤ min(right\_part)，B[j−1]≤A[i] and A[i−1]≤B[j]
	
	> 需要注意在边界值的情况，比如说i=0，j=0，i=m，j=n，此时会数组越界  
	
	>通过更新imin和imax来更新i的值，如果B[j−1]>A[i]说明i太小了，imin=i+1；如果A[i−1]>B[j]说明i太大了，imax=i-1  
	i = (imin + imax) / 2  
	j = (m + n + 1) / 2 - i

	```java
	public double findMedianSortedArrays(int[] nums1, int[] nums2) {
	  int m = nums1.length;
	  int n = nums2.length;
	  if(m > n) {
	    return findMedianSortedArrays(nums2, nums1);
	  }
	  int imin = 0;
	  int imax = m;
	  while(imin <= imax) {
	    int i = (imin + imax) / 2;
	    int j = (m + n + 1) / 2 - i;
	
	    if(i < imax && nums2[j - 1] > nums1[i]) {
	      imin = i + 1;
	    } else if(i > imin && nums1[i - 1] > nums2[j]) {
	      imax = i - 1;
	    } else {
	      int left = 0;
	      if(i == 0) {
	        left = nums2[j - 1];
	      } else if(j == 0) {
	        left = nums1[i - 1];
	      } else {
	        left = Math.max(nums1[i-1], nums2[j-1]);
	      }
	
	      if((m + n) % 2 == 1) {
	        return left;
	      }
	
	      int right;
	      if(i == m) {
	        right = nums2[j];
	      } else if(j == n) {
	        right = nums1[i];
	      } else {
	        right = Math.min(nums1[i], nums2[j]);
	      }
	      return (left + right) / 2.0;
	    }
	  }
	  return 0;
	}
	```

### 5. Longest Palindromic Substring(Medium)

* 题意：求最长的回文子串

* 解法：类似于解法4，不过更加节省空间复杂度，分奇偶讨论，对于当前每一个i，都以第i个字符（奇数）或者第i与i+1个字符（偶数）为中心来查找
* 解法2，使用动态规划，P(i,j)如果回文记为true否则为false

  ```java
  public String longestPalindrome(String s) {
      int maxL = 0;
      int maxLen = s.length() > 0 ? 1 : 0;
      for(int i = 0; i < s.length(); i++) {
          int l = i - 1;
          int h = i + 1;
          int len = 1;
          while(l >= 0 && h < s.length()) {
              if(s.charAt(l) == s.charAt(h)) {
                  len += 2;
                  if(maxLen < len) {
                      maxLen = len;
                      maxL = l;
                  }
                  l--;
                  h++;
              } else {
                  break;
              }
          }
      }
      
      for(int i = 0; i < s.length() - 1; i++) {
          int l = i;
          int h = i + 1;
          int len = 0;
          while(l >= 0 && h < s.length()) {
              if(s.charAt(l) == s.charAt(h)) {
                  len += 2;
                  if(maxLen < len) {
                      maxLen = len;
                      maxL = l;
                  }
                  l--;
                  h++;
              } else {
                  break;
              }
          }
      }
      return s.substring(maxL, maxL + maxLen);
  }
  ```

### 6. ZigZag Conversion(Medium)

* 题意：输入一个字符串，将其按照倒Z字形排好，输出加密结果

* 解法：找到该行对应的下一个的公式，发现其有对称关系，所以下一个字符的位置可以求出

  > 注意numRows为1其对称性会导致死循环  

  > 注意第一排和最后一排对称点会输出两次

* 解法2，依次求出各个字符对应的行，并形成行数组

   ```java
   public String convert(String s, int numRows) {
     String res = "";
     if(numRows == 1)
       return s;
     for(int i = 1; i <= numRows; i++) {
       int temp = i;
       int last = 0;
       int step = 1;
       while(temp <= s.length()) {
         if(temp != last) {
           res = res + s.charAt(temp - 1);
         }
         last = temp;
         temp = (step * numRows - step + 1) * 2 - temp;
         step ++;
       }
     }
     return res;
   }
   ```

### 7. Reverse Integer(Easy)

* 题意：数字反转输出

* 解法：将x从最低位一个个弹出去，结果则不断更新数字，同时计算结果时优先考虑溢出情况

   ```java
   public int reverse(int x) {
     int res = 0;
     while(x != 0) {
       int temp = x % 10;
       x /= 10;
       if(res > Integer.MAX_VALUE/10 || 
          (res == Integer.MAX_VALUE/10 && temp > 7)) {
         return 0;
       }
       if(res < Integer.MIN_VALUE/10 || 
          (res == Integer.MIN_VALUE/10 && temp < -8)) {
         return 0;
       }
       res = res * 10 + temp;
     }
     return res;
   }
   ```

* 解法2：用字符串反转来处理（题中要求只能用数字）

  > 负数先将负号提出来  

  > 如果开头为0记得清掉，并且保证字符串不为空  

  > 还要考虑转化后的数字溢出情况！！！

  ```java
  public int reverse(int x) {
    int isNeg = 1;
    if(x < 0) {
      x = 0 - x;
      isNeg = -1;
    }
    String s = String.valueOf(x);
    String s1 = new StringBuffer(s).reverse().toString();
    while(s1.startsWith("0") && s1.length() > 1) {
      s1 = s1.substring(1, s1.length());
    }
    try {
      int res = Integer.parseInt(s1);
      return res * isNeg;
    } catch(Exception e) {
      return 0;
    }
  }
  ```

### 8. String to Integer (atoi)(Medium)

* 题意：将字符串前缀中的数字提取出来，要求字符串前面可以有任意空格，但是之后必须是正数或者负数，而且数字后可以带任何东西

* 解法：使用有限状态自动机

  > 要注意输出状态异常处理分情况

  > 还要判断结果字符串是否空  

  ```java
  public int myAtoi(String str) {
    int state = 0;
    String resStr = "";
    int isPos = 1;
    boolean isEnd = false;
    for(int i = 0; i < str.length() && !isEnd; i++) {
      switch(state) {
        case 0:
          if(str.charAt(i) == '+') {
            isPos = 1;
            state = 1;
          } else if(str.charAt(i) == '-') {
            isPos = -1;
            state = 1;
          } else if(str.charAt(i) >= '0' && str.charAt(i) <= '9') {
            resStr += str.substring(i, i + 1);
            state = 2;
          } else if(str.charAt(i) != ' ') {
            return 0;
          }
          break;
        case 1:
          if(str.charAt(i) >= '0' && str.charAt(i) <= '9') {
            resStr += str.substring(i, i + 1);
            state = 2;
          } else {
            return 0;
          }
          break;
        case 2:
          if(str.charAt(i) >= '0' && str.charAt(i) <= '9') {
            resStr += str.substring(i, i + 1);
          } else {
            isEnd = true;
          }
          break;
      }
    }
    try {
      if(resStr.length() == 0) {
        return 0;
      }
      int res = Integer.valueOf(resStr) * isPos;
      return res;
    } catch(Exception e) {
      if(isPos > 0) {
        int max = Integer.MAX_VALUE;
        return max;
      } else {
        int min = Integer.MIN_VALUE;
        return min;
      }
    }   
  }
  ```

### 9. Palindrome Number(Medium)

* 题意：判断一个整数是否是回文数（不能转换数字为String或者Integer类型）

* 解法：通过取模和整除，从最低位开始，将该数的后一半翻转过来保存为另一个数temp

	> 判断temp==x（偶数个位）或者temp/10==x（奇数个位）  
	
	```java
	public boolean isPalindrome(int x) {
	  if(x < 0 || (x % 10 == 0 && x != 0)) {
	    return false;
	  } else {
	    int temp = 0;
	    //这里保证一定只分走一半（奇数则多一位）给temp
	    while(x > temp) {
	      temp = temp * 10 + x % 10;
	      x /= 10;
	    }
	    //分奇数和偶数的情况
	    return x == temp || (x == temp / 10);
	  }
	}
	```

### 10. Regular Expression Matching(Hard)

* 题意：判断一个字符串是否匹配指定正则表达式

* 解法：使用递归，关键要找到递归关系式

   ```java
   public boolean isMatch(String s, String p) {
     if(s.length() == 0) {
       if(p.length() == 0) {
         return true;
       } else if(p.length() > 1 && p.charAt(1) == '*') {
         return isMatch(s, p.substring(2, p.length()));
       } else {
         return false;
       }
     } else if(p.length() == 0) {
       return false;
     }
     if(s.charAt(0) == p.charAt(0) || p.charAt(0) == '.') {
       if(p.length() > 1 && p.charAt(1) == '*') {
         return isMatch(s.substring(1, s.length()), p.substring(2, p.length())) || 
           isMatch(s.substring(1, s.length()), p) || 
           isMatch(s, p.substring(2, p.length()));
       } else {
         return isMatch(s.substring(1, s.length()), p.substring(1, p.length()));
       }
     } else {
       if(p.length() > 1 && p.charAt(1) == '*') {
         return isMatch(s, p.substring(2, p.length()));
       } else {
         return false;
       }
     }
   }
   ```

* 解法2：有了递归关系式，可以转化为动态规划，更加节约时间成本

  > 要注意一下边界的取值

  ```java
  class Solution {
      boolean[][] dp;
      public boolean isMatch(String s, String p) {
          dp = new boolean[s.length() + 1][p.length() + 1];
          dp[s.length()][p.length()] = true;
  
          for(int i = s.length() - 1; i >= 0; i--) {
              dp[i][p.length()] = false;
          }
          //初始化j很重要
          for(int j = p.length() - 1; j >= 0; j--) {
              if(j < p.length() - 1 && p.charAt(j+1) == '*') {
                  dp[s.length()][j] = dp[s.length()][j+2];
              } else {
                  dp[s.length()][j] = false;   
              }
          }
          for(int i = s.length() - 1; i >= 0; i--) {
              for(int j = p.length() - 1; j >= 0; j--) {
                  boolean match = (s.charAt(i) == p.charAt(j) || p.charAt(j) == '.');
                  boolean star = (j < p.length() - 1 && p.charAt(j + 1) == '*');
                  
                  boolean first = match && star && 
                      (dp[i+1][j+2] || dp[i][j+2] || dp[i+1][j]);
                  boolean second = match && !star && dp[i+1][j+1];
                  boolean third = !match && star && dp[i][j+2];
                  dp[i][j] = first || second || third;
              }
          }
          return dp[0][0];
      }
  }
  ```

### 11. Container With Most Water(Medium)

* 题意：给定数组，找到面积最大的平台

* 解法：将i和j分别设在数组的两端，计算面积，如果a[i]<a[j]说明a[i]更短，所以i++来弥补短板，否则j--(这里其实是一种贪心的做法)  

* 证明：a[i]->a[j]对应了一个矩阵，假设a[1]<a[6]，说明min(a[1~5]) <= min(a[1], a[6])=a[1]（所以面积肯定会更小），如下

   ```
   	1 2 3 4 5 6  
   1 x ------- o  
   2 x x  
   3 x x x   
   4 x x x x  
   5 x x x x x  
   6 x x x x x x  
   ```

   接下来是a[2]-a[6]，假设a[6]<a[2]，如下：

   	  1 2 3 4 5 6
   	1 x ------- o
   	2 x x       o
   	3 x x x     |
   	4 x x x x   |
   	5 x x x x x |
   	6 x x x x x x

******

```java
public int maxArea(int[] height) {
  int max = 0;
  int i = 0;
  int j = height.length - 1;
  while(j > i) {
    int area = Math.min(height[i], height[j]) * (j - i);
    if(area > max) {
      max = area;
    }
    if(height[i] < height[j]) {
      i++;
    } else {
      j--;
    }
  }
  return max;
}
```

### 12. Integer to Roman(Medium)

* 题意：将4000以内的数字转化为罗马数字

* 解法：按照顺序从高到低依次判断即可，1000，900，500，400，100，90，50，40，10，9，5，4，1
	> 注意1，100，1000要用循环判断
	> 代码太简单暴力不放了


### 13. Roman to Integer(Easy)

* 题意：将4000以内的罗马数字转化为数字

* 解法：使用map映射，并且遍历所有字母，如果当前字母对应value小于下一字母对应的value则计算公式不同


### 14. Longest Common Prefix(Easy)

* 题意：求出字符串数组的最长公共前缀

* 解法：直接按照第一个字符串的字符从前往后一个个判断，当达到字符串长度或者不匹配时返回结果


### 15. 3Sum(Medium)

* 题意：给定数组，求出所有不重复的三元组，每个三元组的和为0

* 解法：a+b+c=0即a+b=-c，所以该题的_**复杂度至少是O(n^2)**_，因此可以_**先将数组排序**_，然后遍历数组所有的值，求出对应相反数Sum，这时问题就转化为了_**2Sum（有序数组**_），所以可以用2个指针分别指向数组首尾，并与Sum比较，依此朝中间移动

   ```java
   public List<List<Integer>> threeSum(int[] nums) {
     List<List<Integer>> res = new ArrayList<>();
     Arrays.sort(nums);
     for(int i = 0; i < nums.length - 2; i++) {
       int sum = -nums[i];
       int j = i + 1;
       int k = nums.length - 1;
       while(j < k) {
         int temp = nums[j] + nums[k];
         if(temp < sum) {
           j++;
         } else if(temp > sum) {
           k--;
         } else {
           List<Integer> a = new ArrayList<>();
           a.add(-sum);
           a.add(nums[j]);
           a.add(nums[k]);
           if(!res.contains(a)) {
             res.add(a);
           }
           j++;
         }
       }
     }
     return res;
   }
   ```


### 16. 3Sum Closest(Medium)

* 题意：给定数组和一个目标值target，求出一个三元组的和，使其最接近target，题目保证只有唯一解

* 解法：和15题一样的思路

   ```java
   public int threeSumClosest(int[] nums, int target) {
     Arrays.sort(nums);
     int diff = Integer.MAX_VALUE;
     for(int i = 0; i < nums.length - 2; i++) {
       int sum = target - nums[i];
       int j = i + 1;
       int k = nums.length - 1;
       int temp;
       while(j < k) {
         temp = nums[j] + nums[k];
         if(temp < sum) {
           j++;
         } else if(temp > sum) {
           k--;
         } else {
           return target;
         }
         if(Math.abs(temp - sum) < Math.abs(diff)) {
           diff = temp - sum;
         }
       }
     }
     return diff + target;
   }
   ```

### 17. Letter Combinations of a Phone Number(Medium)

* 题意：输入一串电话号码(2-9)，输出所有的字母组合(如2->abc)

* 解法：使用队列求解，每次来一个电话号码，就按字母分别联结队列所有元素形成几个新的组合入队，最后将前面的旧组合出队

	> 出队必须在该号码所有字母都遍历完之后
	> 出队时要**注意不能使用计数器**，因为队列长度也会变化

	```java
	public List<String> letterCombinations(String digits) {
	  Map<Integer, Integer> begin = new HashMap<>();
	  Map<Integer, Integer> end = new HashMap<>();
	
	  begin.put(2, 0);
	  end.put(2, 3);
	  for(int i = 3; i <= 9; i++) {
	    int temp = end.get(i - 1);
	    begin.put(i, temp);
	    if(i == 7 || i == 9) {
	      end.put(i, temp + 4);
	    } else {
	      end.put(i, temp + 3);
	    }
	  }
	
	  List<String> res = new ArrayList<>();
	  if(digits.length() > 0) {
	    int num0 = digits.charAt(0) - '0';
	    for(int j = begin.get(num0); j < end.get(num0); j++) {
	      char a = (char)(j + 'a');
	      res.add(new String(new char[]{a}));
	    }
	  }
	
	  for(int i = 1; i < digits.length(); i++) {
	    int num = digits.charAt(i) - '0';
	    int len = res.size();
	    for(int j = begin.get(num); j < end.get(num); j++) {
	      for(int k = 0; k < len; k++) {
	        res.add(res.get(k) + (char)(j + 'a'));
	      }
	    }
	    while(len-- > 0) {
	      res.remove(0);
	    }
	  }
	  return res;
	}
	```

### 18. 4Sumn(Medium)

* 题意：类似之前的3Sum

* 解法：与15题3Sum思路相同


### 19. Remove Nth Node From End of List(Medium)

* 题意：删除单链表的倒数第n个数(保证n有效)

* 解法：由于倒数第n个与顺数第n个对称，所以可以借助头结点到顺数n节点的距离等于尾节点到倒数n节点的距离这一点，_**将倒数变为顺数**_

   ```java
   /*
   	 * Definition for singly-linked list.
   	 * public class ListNode {
   	 *     int val;
   	 *     ListNode next;
   	 *     ListNode(int x) { val = x; }
   	 * }
   */
   class Solution {
     public ListNode removeNthFromEnd(ListNode head, int n) {
       ListNode first = new ListNode(0);
       ListNode second = new ListNode(0);
       first.next = head;
       second.next = head;
       while(n-- > 0) {
         second = second.next;
       }
       while(second.next != null) {
         first = first.next;
         second = second.next;
       }
       if(first.next == head) {
         return head.next;
       }
       first.next = first.next == null ? null : first.next.next;
       return head;
     }
   }
   ```


### 20. Valid Parentheses(Easy)

* 题意：判断有{}()[]组成的字符串是否所有括号有效

* 解法：遍历字符串，使用栈求解，如果{(]则对应})]入栈，否则判断栈顶是否匹配当前字符

   ```java
   class Solution {
     public boolean isValid(String s) {
       Stack<Character> stack = new Stack<Character>();
       for (char c : s.toCharArray()) {
         if (c == '(')
           stack.push(')');
         else if (c == '{')
           stack.push('}');
         else if (c == '[')
           stack.push(']');
         else if (stack.isEmpty() || stack.pop() != c)
           return false;
       }
       return stack.isEmpty();
     }
   }
   ```


### 21. Merge Two Sorted Lists(Easy)

* 题意：给定两个有序链表，合成一个有序链表

* 解法：链表的归并排序，一开始理解错了题意

   ```java
   /**
   	 * Definition for singly-linked list.
   	 * public class ListNode {
   	 *     int val;
   	 *     ListNode next;
   	 *     ListNode(int x) { val = x; }
   	 * }
   	 */
   class Solution {
     public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
       ListNode temp1 = l1;
       ListNode temp2 = l2;
       ListNode res = new ListNode(0);
       res.next = null;
       ListNode temp = res;
       while(temp1 != null && temp2 != null) {
         if(temp1.val < temp2.val) {
           temp.next = temp1;
           temp = temp.next;
           temp1 = temp1.next;
         } else {
           temp.next = temp2;
           temp = temp.next;
           temp2 = temp2.next;
         }
       }
       if(temp1 != null) {
         temp.next = temp1;
       }
       if(temp2 != null) {
         temp.next = temp2;
       }
       return res.next;
     }
   }
   ```


### 22. Generate Parentheses(Medium)

* 题意：给定括号的个数n，求出所有括号都有效的字符串

* 解法：括号只由()组成，所以只要使用**计数器**即可保证字符串一定有效，所以可以使用递归和计数器来判断是补充(还是)即可

   ```java
   class Solution {
     public List<String> generateParenthesis(int n) {
       List<String> ans = new ArrayList();
       backtrack(ans, "", 0, 0, n);
       return ans;
     }
     
     public void backtrack(List<String> ans, String cur, int open, int close, int max){
       if (cur.length() == max * 2) {
         ans.add(cur);
         return;
       }
       if (open < max)
         backtrack(ans, cur+"(", open+1, close, max);
       if (close < open)
         backtrack(ans, cur+")", open, close+1, max);
     }
   }
   ```

* 解法2：每个有效字符串都可以表示为(...)或者(...)(...)...，所以可以将字符串拆分为左右两部分，递归求出，然后再合并为结果

  > 注意这里要实现(left)，否则所有形式都会是()()...  
  > 所以left要有初始值为""，即n=0时的结果

  ```java
  class Solution {
    public List<String> generateParenthesis(int n) {
      List<String> res = new ArrayList<>();
      if(n == 0) {
        res.add("");
      }
      for(int i = 0; i < n; i++) {
        for(String left: generateParenthesis(i)) {
          for(String right: generateParenthesis(n-i-1)) {
            res.add("(" + left + ")" + right);
          }
        }
      }
      return res;
    }
  }
  ```

### 23. Merge k Sorted Lists(Hard)

* 题意：给定k个有序的链表(长度统一记为n)，合成一个有序的链表

* 解法：暴力解法，找n*k次最小值加入到结果链表中

   ```java
   /**
   	 * Definition for singly-linked list.
   	 * public class ListNode {
   	 *     int val;
   	 *     ListNode next;
   	 *     ListNode(int x) { val = x; }
   	 * }
   	 */
   class Solution {
     public ListNode mergeKLists(ListNode[] lists) {
       int n = lists.length;
       ListNode res = new ListNode(0);
       res.next = null;
       ListNode temp = res;
       while(true) {
         int mini = -1;
         int min = Integer.MAX_VALUE;
         for(int i = 0; i < lists.length; i++) {
           if(lists[i] != null) {
             if(lists[i].val < min) {
               mini = i;
               min = lists[i].val;
             }
           }
         }
         if(mini > -1) {
           ListNode next = lists[mini].next;
           temp.next = lists[mini];
           lists[mini] = lists[mini].next;
           temp = temp.next;
         } else {
           break;
         }
       }
       return res.next;
     }
   }
   ```

* 解法2：使用优先队列来进行最小值的更新，维护一个优先队列（小根堆），队列的插入和删除复杂度都为O(logn)，进行n*k次操作即可  

   ```java
   class Solution {
     public ListNode mergeKLists(ListNode[] lists) {
       ListNode res = new ListNode(0);
       res.next = null;
       ListNode temp = res;
   
       if(lists.length == 0)
         return null;
   
       PriorityQueue<ListNode> queue = new PriorityQueue<ListNode>(lists.length, new Comparator<ListNode>() { public int compare(ListNode l1, ListNode l2) { return l1.val - l2.val; }});
   
       for(int i = 0; i < lists.length; i++) {
         if(lists[i] != null) {
           queue.add(lists[i]);
         }
       }
   
       while(!queue.isEmpty()) {
         ListNode now = queue.poll();
   
         temp.next = now;
         temp = temp.next;
         now = now.next;
   
         if(now != null) {
           queue.add(now);
         }
       }
   
       return res.next;
     }
   }
   ```

* 解法3：分治法，将链表拆分成两两一组，进行logn次操作后即可得到结果

### 24. Swap Nodes in Pairs(Medium)

* 题意：交换链表中相邻的结点

* 解法：先在纸上模拟交换的过程，然后实现即可（很简单）


### 25. Reverse Nodes in k-Group(Hard)

* 题意：每k个一组反转链表结点

* 解法：使用结点存储每一组的开头结尾，然后内部遍历使用***头插法***

   ```java
   /**
   	 * Definition for singly-linked list.
   	 * public class ListNode {
   	 *     int val;
   	 *     ListNode next;
   	 *     ListNode(int x) { val = x; }
   	 * }
   	 */
   class Solution {
     public ListNode reverseKGroup(ListNode head, int k) {  
       ListNode res = new ListNode(0);
       res.next = head;
       ListNode temp = head;
       int n = 0;
       while(temp != null) {
         n++;
         temp = temp.next;
       };
   
       ListNode now = res;
       for(int i = 0; i < n / k; i++) {
         ListNode end = now.next;
         for(int j = 1; j < k; j++) {
           ListNode tmp = end.next;
           ListNode next = tmp.next;
           ListNode first = now.next;
           now.next = tmp;
           tmp.next = first;
           end.next = next;
         }
         now = end;
       }
       return res.next;
     }
   }
   ```


### 26. Remove Duplicates from Sorted Array(Easy)

* 题意：去掉一个有序数组的重复数字，要求返回独特数字长度n，并将其放在数组前n个位置，空间复杂度要求O(1)

* 解法：使用一个变量n记住当前独特数字的位置，遍历一遍更新独特数字即可

   ```java
   class Solution {
     public int removeDuplicates(int[] nums) {
       if(nums.length == 0) {
         return 0;
       }
       int n = 1;
       int x = nums[0];
       for(int i = 1; i < nums.length;) {
         if(nums[i] == x) {
           i++;
         } else {
           nums[n++] = nums[i];
           x = nums[i];
         }
       }
       return n;
     }
   }
   ```


### 27. Remove Element(Easy)

* 题意：给定数组，移除所有值为val的元素，并返回数组长度

* 解法：用一个n表示当前数组元素，遍历数组，如果当前不是val那么就放入nums[n++]中

   ```java
   class Solution {
     public int removeElement(int[] nums, int val) {
       int n = 0;
       for(int i = 0; i < nums.length; i++) {
         if(nums[i] != val) {
           nums[n++] = nums[i];
         }
       }
       return n;
     }
   }
   ```


### 28. Implement strStr()(Easy)

* 题意：给定字符串和模式串，实现字符串匹配函数，返回匹配起始位置，不匹配返回-1，模式串为空返回0

* 解法：使用KMP算法求解（不使用库函数）  

   ```java
   class Solution {
     public int strStr(String haystack, String needle) {
       if(needle.length() == 0) {
         return 0;
       }
       //求next数组
       int[] next = new int[needle.length()];
       next[0] = -1;
       int j = 0;
       int k = -1;
       while(j < needle.length() - 1) {
         if(k == -1 || needle.charAt(j) == needle.charAt(k)) {
           //说明j之前与k之前的所有字符都一样
           next[++j] = ++k;
         } else {
           //否则k返回上个位置
           k = next[k];
         }
       }
   
       //字符串匹配
       int i = 0;
       j = 0;
       while(i < haystack.length() && j < needle.length()) {
         if (j == -1 || haystack.charAt(i) == needle.charAt(j)) {
           i++;
           j++;
         } else {
           j = next[j];
         }
       }
       if(j == needle.length()) {
         return i - j;
       } else {
         return -1;
       }
     }
   }
   ```


### 29. Divide Two Integers(Medium)

* 题意：实现int型的除法，不能使用long和乘除取模运算

* 解法：使用移位运算求解，res = 2^a + 2^b + ...，遍历pow-0即可，对除数divisor进行移位遍历

  > 注意负数的范围大一些，所以都要转为负数，这里有一种特殊情况  
  > 注意要初始化pow值，否则会导致溢出，这里使用正数求pow，所以也有了一种特殊情况，同时还要注意除数是否可以被2整除

  ```java
  class Solution {
    public int divide(int dividend, int divisor) {
      if(dividend == Integer.MIN_VALUE && divisor == -1) {
        return Integer.MAX_VALUE;
      }
      if(divisor == Integer.MIN_VALUE) {
        if(dividend == Integer.MIN_VALUE) {
          return 1;
        } else {
          return 0;
        }
      }
  
      //make the both numbers negative
      //dividend = min and divisor == -1 is overflow as a special case as above
      boolean sign = true;
      if(dividend > 0) {
        dividend = 0 - dividend;
      } else {
        sign = !sign;
      }
      if(divisor > 0) {
        divisor = 0 - divisor;
      } else {
        sign = !sign;
      }
  
      //initial pow by using abs(divisor)
      //therefore divisor = min needs to be considered specially as above
      //different situation: is the divisor divided by 2?
      int res = 0;
      int pow = 32;
      int isDividedBy2 = 1;
      int div = Math.abs(divisor);
      while(div != 0) {
        if((div & 1) > 0) {
          if(isDividedBy2 == 1) {
            isDividedBy2 = 0;
          } else if(isDividedBy2 == 0) {
            isDividedBy2 = -1;
          }
        }
        div >>= 1;
        pow--;
      }
      pow += isDividedBy2;
  
      //res = 2^a + 2^b + ... 
      while(dividend <= divisor) {
        while(dividend > (divisor << pow)) {
          pow--;
        }
        res += (1 << pow);
        dividend -= (divisor << pow);
        pow--;
      }
  
      return sign ? res : 0 - res;
    }
  }
  ```

### 30. Substring with Concatenation of All Words(Hard)

* 题意：给定一个字符串s，给定n个单词words(长度都一样为len)，找出所有子串的开始下标，使得子串包含了给定的所有单词，顺序可以不对应。如果有重复的单词，比如有["foo", "foo"]那么子串也必须含有两个"foo"，也就是说个数必须相同

* 解法：使用两个HashMap解决，一个保存words里面单词对应出现次数，另一个保存当前子串出现单词次数，要保证该次数不超过对应次数，使用单词总数来判断是否匹配。从0开始，每次移动一个单词长度len，所以要循环len次来计算所有情况。其中，又有了3种情况可以[优化](https://leetcode.windliang.cc/leetCode-30-Substring-with-Concatenation-of-All-Words.html)：

  > * 子串匹配，移动到下一个子串时，前面的单词可以不用再判断，而只要判断新加入的单词  
  > * 判断时出现不匹配单词，直接将下标移至该单词后面  
  > * 判断时单词次数超过了，一直移除第一个单词，直至该单词可以加入  

  ```java
  public List<Integer> findSubstring(String s, String[] words) {
    List<Integer> res = new ArrayList<Integer>();
    int wordNum = words.length;
    if (wordNum == 0) {
      return res;
    }
    int wordLen = words[0].length();
    HashMap<String, Integer> allWords = new HashMap<String, Integer>();
    for (String w : words) {
      int value = allWords.getOrDefault(w, 0);
      allWords.put(w, value + 1);
    }
    //将所有移动分成 wordLen 类情况
    for (int j = 0; j < wordLen; j++) {
      HashMap<String, Integer> hasWords = new HashMap<String, Integer>();
      int num = 0; //记录当前 HashMap2（这里的 hasWords 变量）中有多少个单词
      //每次移动一个单词长度
      for (int i = j; i < s.length() - wordNum * wordLen + 1; i = i + wordLen) {
        boolean hasRemoved = false; //防止情况三移除后，情况一继续移除
        while (num < wordNum) {
          String word = s.substring(i + num * wordLen, i + (num + 1) * wordLen);
          if (allWords.containsKey(word)) {
            int value = hasWords.getOrDefault(word, 0);
            hasWords.put(word, value + 1);
            //出现情况三，遇到了符合的单词，但是次数超了
            if (hasWords.get(word) > allWords.get(word)) {
              // hasWords.put(word, value);
              hasRemoved = true;
              int removeNum = 0;
              //一直移除单词，直到次数符合了
              while (hasWords.get(word) > allWords.get(word)) {
                String firstWord = s.substring(i + removeNum * wordLen, i + (removeNum + 1) * wordLen);
                int v = hasWords.get(firstWord);
                hasWords.put(firstWord, v - 1);
                removeNum++;
              }
              num = num - removeNum + 1; //加 1 是因为我们把当前单词加入到了 HashMap 2 中
              i = i + (removeNum - 1) * wordLen; //这里依旧是考虑到了最外层的 for 循环，看情况二的解释
              break;
            }
            //出现情况二，遇到了不匹配的单词，直接将 i 移动到该单词的后边（但其实这里
            //只是移动到了出现问题单词的地方，因为最外层有 for 循环， i 还会移动一个单词
            //然后刚好就移动到了单词后边）
          } else {
            hasWords.clear();
            i = i + num * wordLen;
            num = 0;
            break;
          }
          num++;
        }
        if (num == wordNum) {
          res.add(i);
  
        }
        //出现情况一，子串完全匹配，将上一个子串的第一个单词从 HashMap2 中移除
        if (num > 0 && !hasRemoved) {
          String firstWord = s.substring(i, i + wordLen);
          int v = hasWords.get(firstWord);
          hasWords.put(firstWord, v - 1);
          num = num - 1;
        }
  
      }
  
    }
    return res;
  }
  ```


### 31. Next Permutation(Medium)

* 题意：给定一个数组表示一个序列，将该序列修改为其下一个字典序序列(最大字典序列下一个是最小字典序列)，空间复杂度为O(1)

* 解法：先找到一个notReverseIndex，在这之后的子序列一定满足降序，而notReverseIndex不满足。为了找到下一个字典序列，从子序列中找到一个刚好大于notReverseIndex值的数字(即下一个字典序列第一个不同元素)，将它们交换，然后再使用左右交换将notReverseIndex后的最大序列变为最小序列即可

   ```java
   class Solution {
     public void nextPermutation(int[] nums) {
       int notReverseIndex = -1;
       for(int i = nums.length - 2; i >= 0; i--) {
         if(nums[i] < nums[i+1]) {
           notReverseIndex = i;
           break;
         }
       }
       if(notReverseIndex != -1) {
         int notReverseNum = nums[notReverseIndex];
         for(int i = nums.length - 1; i > notReverseIndex; i--) {
           if(nums[i] > notReverseNum) {
             nums[notReverseIndex] = nums[i];
             nums[i] = notReverseNum;
             break;
           }
         }
       }
   
       int len = nums.length - notReverseIndex - 1;
       for(int i = 0; i < len/2 ; i++) {
         int temp = nums[i + nums.length - len];
         nums[i + nums.length - len] = nums[nums.length - i - 1];
         nums[nums.length - i - 1] = temp;
       }
   
     }
   }
   ```


### 32. Longest Valid Parentheses(Medium)

* 题意：求出最长的有效括号长度

* 解法1：动态规划，有两种情况:
 	> dp[i-2]**(**)  
	> dp[i-dp[i-1]-2]**(**dp[i-1]**)**

```java
public int longestValidParentheses(String s) {
  int maxans = 0;
  int dp[] = new int[s.length()];
  for (int i = 1; i < s.length(); i++) {
    if (s.charAt(i) == ')') {
      if (s.charAt(i - 1) == '(') {
        dp[i] = (i > 2 ? dp[i - 2] : 0) + 2;
      } else if (i - dp[i - 1] > 0 && s.charAt(i - dp[i - 1] - 1) == '(') {
        dp[i] = dp[i - 1] + ((i - dp[i - 1]) > 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
      }
      maxans = Math.max(maxans, dp[i]);
    }
  }
  return maxans;
}
```
* 解法2：使用栈来存储“(”的下标，之后匹配到“)”时出栈，然后下标减去栈顶值即可求出当前最长的匹配长度。匹配到“)”出栈，每当栈为空时，要将栈底设置为当前下标，所以要初始化栈底为-1

  ```java
  public int longestValidParentheses(String s) {
    int maxans = 0;
    Stack<Integer> stack = new Stack<>();
    stack.push(-1);
    for (int i = 0; i < s.length(); i++) {
      if (s.charAt(i) == '(') {
        stack.push(i);
      } else {
        stack.pop();
        if (stack.empty()) {
          stack.push(i);
        } else {
          maxans = Math.max(maxans, i - stack.peek());
        }
      }
    }
    return maxans;
  }
  ```

* 解法3：使用left和right来分别记住“(”和“)”，从左至右遍历一遍，每当left=right时更新max，如果right>left，则一起置零；再类似地从右至左遍历一遍，以保证())(()()情况可以识别成功

  > 分两种情况是用来判断是缺少了“(”还是“)”

  ```java
  public int longestValidParentheses(String s) {
    int left = 0, right = 0, maxlength = 0;
    for (int i = 0; i < s.length(); i++) {
      if (s.charAt(i) == '(') {
        left++;
      } else {
        right++;
      }
      if (left == right) {
        maxlength = Math.max(maxlength, 2 * right);
      } else if (right >= left) {
        left = right = 0;
      }
    }
    left = right = 0;
    for (int i = s.length() - 1; i >= 0; i--) {
      if (s.charAt(i) == '(') {
        left++;
      } else {
        right++;
      }
      if (left == right) {
        maxlength = Math.max(maxlength, 2 * left);
      } else if (left >= right) {
        left = right = 0;
      }
    }
    return maxlength;
  }
  ```

### 33. Search in Rotated Sorted Array(Medium)

* 题意：一个有序数组(不重复)，将其分为前后两部分，并整体交换两部分的位置，给定目标值，实现二分查找

* 解法：数组旋转后，一定能保证至少有一半元素是有序的，这样就可以判断目标值在哪一半里面了

  ```java
  public int search(int[] nums, int target) {
    int start = 0;
    int end = nums.length - 1;
    while (start <= end) {
      int mid = (start + end) / 2;
      if (nums[mid] == target)
        return mid;
  
      if (nums[start] <= nums[mid]){
        if (target < nums[mid] && target >= nums[start]) {
          end = mid - 1;
        } else {
          start = mid + 1;
        }
      }
  
      if (nums[mid] <= nums[end]){
        if (target > nums[mid] && target <= nums[end]) {
          start = mid + 1;
        } else {
          end = mid - 1;
        }
      }
    }
    return -1;
  }
  ```


### 34. Find First and Last Position of Element in Sorted Array(Medium)

* 题意：给定有序数组(可重复)，找出目标值的起始和终止索引

* 解法：先找first，进行二分搜索，如果中点等于目标值，那么更新first，并**继续向前搜索**；类似地找last，匹配上则**继续向后搜索**

  ```java
  public int[] searchRange(int[] nums, int target) {
    int first = -1;
    int last = -1;
  
    int start = 0;
    int end = nums.length - 1;
    while(start <= end) {
      int mid = (start + end) / 2;
      if(nums[mid] == target && (first < 0 || first > mid)) {
        first = mid;
      }
      if(nums[mid] >= target) {
        end = mid - 1;
      } else {
        start = mid + 1;
      }
    }
  
    start = 0;
    end = nums.length - 1;
    while(start <= end) {
      int mid = (start + end) / 2;
      if(nums[mid] == target && last < mid) {
        last = mid;
      }
      if(nums[mid] <= target) {
        start = mid + 1;
      } else {
        end = mid - 1;
      }
    }
    return new int[]{first, last};
  }
  ```


### 35. Search Insert Position(Easy)

* 题意：给定有序数组和目标值，求插入位置或者匹配索引

* 解法：二分法匹配，如果没有匹配上，则返回最后的二分搜索起始点


### 36. Valid Sudoku(Medium)

* 题意：判断数独题目是否有效，每行每列为1-9，9个小九宫格也为1-9

* 解法：本质是查重问题，所以使用HashSet

  ```java
  public boolean isValidSudoku(char[][] board) {
    HashSet<String> set = new HashSet();
    for(int i = 0; i < board.length; i++) {
      for(int j = 0; j < board[i].length; j++) {
        if(board[i][j] != '.') {
          String b = "(" + board[i][j] + ")";
          if (!set.add(b + i) || !set.add(j + b) || !set.add(i/3 + b + j/3)) {
            return false;
          }
        }
      }
    }
    return true;
  }
  ```

### 37. Sudoku Solver(Hard)

* 题意：数独解题

* 解法：使用回溯法求解，先判断是否有效，如果有效再判断是否可以解题，如果不能则回溯，使用递归来实现

  ```java
  public void solveSudoku(char[][] board) {
    solve(board);
  }
  
  public boolean solve(char[][] board) {
    for(int i = 0; i < board.length; i++) {
      for(int j = 0; j < board[i].length; j++) {
        if(board[i][j] == '.') {
          for(char c = '1'; c <= '9'; c++) {
            if(isValid(board, i, j, c)) {
              board[i][j] = c;
              if(solve(board)) {
                return true;
              } else {
                board[i][j] = '.';
              }
            }
          }
          return false;
        }
      }
    }
    return true;
  }
  
  public boolean isValid(char[][] board, int i, int j, char c) {
    for(int k = 0; k < board.length; k++) {
      if(board[i][k] == c || board[k][j] == c) {
        return false;
      }
    }
    for(int k = (i/3) * 3; k < (i/3) * 3 + 3; k++) {
      for(int l = (j/3) * 3; l < (j/3) * 3 + 3; l++) {
        if(board[k][l] == c) {
          return false;
        }
      }
    }
    return true;
  }
  ```

### 38. Count and Say(Easy)

* 题意：从"1"开始，下一个字符串是上一个字符串的读法，1个1为11，由于1连续，所以再下一个是2个1为21，求n次过后的结果

* 解法：暴力法，一直求下去

  > 注意字符串加上两个char时，要加上""来隔开两个char

  ```java
  public String countAndSay(int n) {
    String last = "1";
    while(n-- > 1) {
      String temp = "";
      char num = '0';
      char now = ' ';
      for(int j = 0; j < last.length(); j++) {
        if(last.charAt(j) == now) {
          num++;
        } else {
          if(now != ' ') {
            temp += num + "" + now;
          }
          now = last.charAt(j);
          num = '1';
        }
      }
      if(now != ' ') {
        temp += num + "" + now;
      }
      last = temp;
      temp = "";
    }
    return last;
  }
  ```


### 39. Combination Sum(Medium)

* 题意：给定无重复元素的数组和目标值，使用数组里的值让其和凑成目标值，可使用同一元素，求出所有的结果，结果不能重复

* 解法：使用递归不能保证不重复，所以使用回溯法来避免

  ```java
  public List<List<Integer>> combinationSum(int[] nums, int target) {
    Arrays.sort(nums);
    List<List<Integer>> list = new ArrayList<>();
    backtrack(list, new ArrayList<>(), nums, target, 0);
    return list;
  }
  
  private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int remain, int start) {
    if(remain < 0) {
      return;
    } else if(remain == 0) {
      list.add(new ArrayList<>(tempList));
    } else {
      for(int i = start; i < nums.length; i++) {
        tempList.add(nums[i]);
        // not i + 1 because we can reuse same elements
        backtrack(list, tempList, nums, remain - nums[i], i);
        tempList.remove(tempList.size() - 1);
      }
    }
  }
  ```


### 40. Combination Sum II(Medium)

* 题意：给定可重复元素的数组和目标值，使用数组里的值让其和凑成目标值，每个元素只能使用一次，求出所有的结果，结果不能重复

* 解法：和上题解法类似，只是由于数组元素可重复，所以需要在循环里面判断当前元素是否等于上一元素，否则会结果重复

  ```java
  public List<List<Integer>> combinationSum2(int[] nums, int target) {
    Arrays.sort(nums);
    List<List<Integer>> list = new ArrayList<>();
    backtrack(list, new ArrayList<>(), nums, target, 0);
    return list;
  }
  
  private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, int remain, int start) {
    if(remain < 0) {
      return;
    } else if(remain == 0) {
      list.add(new ArrayList<>(tempList));
    } else {
      for(int i = start; i < nums.length; i++) {
        if(i > start && nums[i] == nums[i - 1]) {
          continue;
        }
        tempList.add(nums[i]);
        backtrack(list, tempList, nums, remain - nums[i], i + 1);
        tempList.remove(tempList.size() - 1);
      }
    }
  }
  ```

### 41. First Missing Positive(Hard)

* 题意：给定数组，求出数组中缺少的第一个正数

* 解法：将数组中的正数***放到其对应位置上***，再遍历一遍即可判断，所以使用交换函数来进行安排
  > 数组中，求某一个固定顺序即可用固定位置来解

  ```java
  public int firstMissingPositive(int[] nums) {
    int i = 0;
    while(i < nums.length) {
      if(nums[i] > 0 && nums[i] <= nums.length && nums[i] != nums[nums[i] - 1]) {
        swap(nums, i, nums[i] - 1);
      } else {
        i++;
      }
    }
    i = 0;
    while(i < nums.length) {
      if(nums[i] != i + 1) {
        return i + 1;
      }
      i++;
    }
    return i + 1;
  }
  ```


### 42. Trapping Rain Water(Hard)

* 题意：给定数组，画成柱状图，求出下雨后多少雨水会堆在坑里
![Alt text](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)

* 解法：从图中可以看出，装了水后的柱状图就变成了“山”字形，先增大后减小，所以***使用首尾指针***，然后左右各维护一个当前最大值，比较每个元素与左右最大值的情况，并计算出水的面积

  > 还要判断左右两边的值哪个更小，小的值优先更新，保证不会出现最后左边大的值更新了右边小的值的情况

  ```java
  public int trap(int[] height) {
    int max_left = 0;
    int max_right = 0;
    int left = 0;
    int right = height.length - 1;
    int sum = 0;
    while(left <= right) {
      if(height[left] < height[right]) {
        if(max_left < height[left]) {
          max_left = height[left];
        } else {
          sum += max_left - height[left];
        }
        left++;
      } else {
        if(max_right < height[right]) {
          max_right = height[right];
        } else {
          sum += max_right - height[right];
        }
        right--;
      }
    }
    return sum;
  }
  ```


### 43. Multiply Strings(Medium)

* 题意：实现字符串的乘法

* 解法：每两个数字之间相乘，将结果保存到一个一维数组中，每进行一次乘法，都要更新结果的数组，而且乘法结果和下标的关系很大

  > 如下图的4*3和5\*2就是在同一层上，所以i+j与i+j+1即可以表示在结果数组中的位置

![Alt text](https://drscdn.500px.org/photo/130178585/m%3D2048/300d71f784f679d5e70fadda8ad7d68f)


```java
public String multiply(String num1, String num2) {
  int m = num1.length();
  int n = num2.length();
  int[] pos = new int[m + n];
  for(int i = 0; i < m + n; i++) {
    pos[i] = 0;
  }

  for(int i = m - 1; i >= 0; i--) {
    for(int j = n - 1; j >= 0; j--) {
      int mul = (num1.charAt(i) - '0') * (num2.charAt(j) - '0'); 
      int p1 = i + j;
      int p2 = i + j + 1;
      int sum = mul + pos[p2];

      pos[p1] += sum / 10;
      pos[p2] = sum % 10;
    }
  }  

  StringBuilder sb = new StringBuilder();
  for(int p : pos) {
    //排除前导0
    if(!(sb.length() == 0 && p == 0)) {
      sb.append(p);
    }
  }
  return sb.length() == 0 ? "0" : sb.toString();
}
```

### 44. Wildcard Matching(Hard)

* 题意：字符串匹配表达式

* 解法：类似第10题的正则表达式匹配，使用动态规划，注意要初始化dp[s.length()] [j]

  > 匹配到*时分为三种情况：丢掉当前，丢掉当前和\*，丢掉\*

  ```java
  boolean[][] dp;
  public boolean isMatch(String s, String p) {
    dp = new boolean[s.length() + 1][p.length() + 1];
    dp[s.length()][p.length()] = true;
  
    for(int i = s.length() - 1; i >= 0; i--) {
      dp[i][p.length()] = false;
    }
    for(int j = p.length() - 1; j >= 0; j--) {
      if(p.charAt(j) == '*') {
        dp[s.length()][j] = dp[s.length()][j+1];
      } else {
        dp[s.length()][j] = false;
      }
    }
    for(int i = s.length() - 1; i >= 0; i--) {
      for(int j = p.length() - 1; j >= 0; j--) {
        if(s.charAt(i) == p.charAt(j) || p.charAt(j) == '?') {
          dp[i][j] = dp[i+1][j+1];
        } else if(p.charAt(j) == '*') {
          dp[i][j] = dp[i+1][j+1] || dp[i+1][j] || dp[i][j+1];
        } else {
          dp[i][j] = false;
        }
      }
    }
    return dp[0][0];
  }
  ```

### 45. Jump Game II(Hard)

* 题意：给定数组，每个元素表示可以跳几步，求从数组头到数组尾的最小步数

* 解法：实际上使用了BFS的思想（***贪心，每次都走到最远处***），将当前能达到的最远节点计算出来，每当达到边界即为走一步

		例：2 3 1 1 4  
		BFS后
		2  
		3 1  
		1 4
******

```java
public int jump(int[] nums) {
  int end = 0;
  int maxPos = 0;
  int steps = 0;
  for(int i = 0; i < nums.length - 1; i++) {
    maxPos = Math.max(maxPos, nums[i] + i);
    if(i == end) {
      end = maxPos;
      steps++;
    }
  }
  return steps;
}
```

### 46. Permutations(Medium)

* 题意：给定数组（无重复元素），求出所有的排列

* 解法：使用回溯法，同时判断元素是否已经使用过

  ```java
  public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    backtrack(list, new ArrayList<>(), nums);
    return list;
  }
  
  private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums) {
    if(tempList.size() == nums.length) {
      list.add(new ArrayList<>(tempList));
      return;
    }
  
    for(int i = 0; i < nums.length; i++) {
      if(!tempList.contains(nums[i])) {
        tempList.add(nums[i]);
        backtrack(list, tempList, nums);
        tempList.remove(tempList.size() - 1);
      }
    }
  }
  ```



### 47. Permutations II(Medium)

* 题意：给定数组（有重复元素），求出所有独特的排列

* 解法：回溯法，这里需要限制一下，如果是当前元素与之前元素相等，那么只有之前元素使用过后才可以使用当前元素，这样保证了同一元素的顺序，排列才会独特，因此也需要一个used数组来判断是否使用过某一元素

  ```java
  public List<List<Integer>> permuteUnique(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> list = new ArrayList<>();
    boolean[] used = new boolean[nums.length];
    for(int i = 0; i < nums.length; i++) {
      used[i] = false;
    }
    backtrack(list, new ArrayList<>(), nums, used);
    return list;
  }
  
  private void backtrack(List<List<Integer>> list, List<Integer> tempList, int [] nums, boolean[] used) {
    if(tempList.size() == nums.length) {
      list.add(new ArrayList<>(tempList));
      return;
    }
  
    for(int i = 0; i < nums.length; i++) {
      if(!used[i] && !(i > 0 && nums[i] == nums[i-1] && !used[i - 1])) {
        tempList.add(nums[i]);
        used[i] = true;
        backtrack(list, tempList, nums, used);
        tempList.remove(tempList.size() - 1);
        used[i] = false;
      }
    }
  }
  ```


### 48. Rotate Image(Medium)

* 题意：给定二维数组，将数组顺时针旋转90度，不能使用另一个数组

* 解法：将旋转分解为一个上下对称和一个斜对称，交换元素


### 49. Group Anagrams(Medium)

* 题意：给定字符串数组，按照使用字母相同来分组

* 解法：***分组问题，可以使用map来进行组的划分***，分组依据是使用字母相同，所以key就是按照这个来生成，这里使用了字符串的最小字典序当做key；一种更好的解法是对26个字母进行计数，然后使用分割符将26个计数器组装起来成为一个唯一的字符串当做key，空间换时间

  ```java
  public List<List<String>> groupAnagrams(String[] strs) {
    List<List<String>> res = new ArrayList<>();
    Map<String, Integer> dic = new HashMap();
    for(int i = 0; i < strs.length; i++) {
      String str = getDictionaryStr(strs[i]);
      if(dic.containsKey(str)) {
        res.get(dic.get(str)).add(strs[i]);
      } else {
        dic.put(str, res.size());
        List<String> temp = new ArrayList<>();
        temp.add(strs[i]);
        res.add(temp);
      }
    }
    return res;
  }
  
  private String getDictionaryStr(String str) {
    char[] res = str.toCharArray();
    Arrays.sort(res);
    return new String(res);
  }
  ```

### 50. Pow(x, n)(Medium)

* 题意：实现乘方，x是double型且-100<x<100，n是int型

* 解法：使用递归即可完成，注意0的0次方为1，还要注意int的最小值转为正数会溢出

  ```java
  public double myPow(double x, int n) {
    if(n == 0) {
      return 1;
    } else if(n < 0) {
      if(n == Integer.MIN_VALUE) {
        double res = myPow(1/x, -(n/2));
        return res * res;
      }
      return myPow(1/x, -n);
    } else if(n == 1) {
      return x;
    } else if(n == 2) {
      return x * x;
    }
    int temp = n / 2;
    int remain = n % 2;
    double res = myPow(x, temp);
    res *= res;
    return remain == 0 ? res : res * x;
  }
  ```


### 51. N-Queens(Hard)

* 题意：n皇后问题，皇后不能出现在同一直线或斜线上，给定n，求出所有解

* 解法：使用回溯法，并用布尔数组记录该条线上是否有皇后，需要3个布尔数组：逐行添加皇后，判断当前列，判断当前左斜线，判断当前右斜线

  ```java
  public List<List<String>> solveNQueens(int n) {
    boolean[] y_used = new boolean[n];
    boolean[] sl_used = new boolean[2 * n];
    boolean[] sr_used = new boolean[2 * n];
    List<List<String>> res = new ArrayList<>();
    List<String> temp = new ArrayList<>();
    String now = "";
    for(int i = 0; i < n; i++) {
      y_used[i] = false;
      sl_used[i] = false;
      sl_used[i + n] = false;
      sr_used[i] = false;
      sr_used[i + n] = false;
      now += ".";
    }
    for(int i = 0; i < n; i++) {
      temp.add(now);
    }
  
    backtrack(res, temp, 0, n, y_used, sl_used, sr_used);
    return res;
  }
  
  private void backtrack(List<List<String>> list, List<String> temp, int num, int n, boolean[] y_used, boolean[] sl_used, boolean[] sr_used) {
    if(num == n) {
      list.add(new ArrayList<>(temp));
      return;
    }
    for(int i = 0; i < n; i++) {
      if(!y_used[i] && !sl_used[num - i + n - 1] && !sr_used[num + i]) {
        String str = temp.get(num);
        str = str.substring(0, i) + "Q" + str.substring(i + 1);
        temp.set(num, str);
        y_used[i] = true;
        sl_used[num - i + n - 1] = true;
        sr_used[num + i] = true;
  
        backtrack(list, temp, num + 1, n, y_used, sl_used, sr_used);
  
        str = str.substring(0, i) + "." + str.substring(i + 1);
        temp.set(num, str);
        y_used[i] = false;
        sl_used[num - i + n - 1] = false;
        sr_used[num + i] = false;
      }
    }
  }
  ```


### 52. N-Queens II(Hard)

* 题意：求出n皇后有多少种解法

* 解法：和上题解法一样


### 53. Maximum Subarray(Easy)

* 题意：给定数组，求出最大的子数组和（至少有一个元素）

* 解法：贪心法，遍历数组，如果加上当前元素，sum<0则sum置零，否则用sum来更新max_sum

  > 特殊情况：数组全部为负数，则输出最大的负数

  ```java
  public int maxSubArray(int[] nums) {
    int sum = 0;
    int max_sum = -1;
    for(int i = 0; i < nums.length; i++) {
      sum += nums[i];
      if(sum < 0) {
        sum = 0;
      } else if(sum > max_sum) {
        max_sum = sum;
      }
    }
    if(max_sum < 0) {
      max_sum = Integer.MIN_VALUE;
      for(int i = 0; i < nums.length; i++) {
        if(nums[i] > max_sum) {
          max_sum = nums[i];
        }
      }
    }
    return max_sum;
  }
  ```

### 54. Spiral Matrix(Medium)

* 题意：将一个矩阵螺旋输出

* 解法：模拟螺旋的过程，即四个循环，使用distant表示当前的圈数，并用一个计数器来判断是否遍历完矩阵

* 解法2：模拟螺旋过程，用数字表示当前方向行走方向，使用布尔数组来判断当前元素是否已经使用过，如果使用过则表示到达边界，需要转弯

  ```java
  public List<Integer> spiralOrder(int[][] matrix) {
    List<Integer> res = new ArrayList<>();
    if(matrix.length == 0) {
      return res;
    }
    int n = matrix.length * matrix[0].length;
    int distant = 0;
    while(true) {
      for(int i = distant; i < matrix[0].length - distant; i++) {
        res.add(matrix[distant][i]);
        if(--n == 0) {
          return res;
        }
      }
      for(int i = distant + 1; i < matrix.length - distant; i++) {
        res.add(matrix[i][matrix[0].length - distant - 1]);
        if(--n == 0) {
          return res;
        }
      }
      for(int i = matrix[0].length - distant - 2; i >= distant; i--) {
        res.add(matrix[matrix.length - distant - 1][i]);
        if(--n == 0) {
          return res;
        }
      }
      for(int i = matrix.length - distant - 2; i > distant; i--) {
        res.add(matrix[i][distant]);
        if(--n == 0) {
          return res;
        }
      }
      distant++;
    }
  }
  ```

### 55. Jump Game(Medium)

* 题意：跳跃游戏，第45题的简化版，给定数组，元素表示能够跳跃的格子数，判断是否可以到达终点

* 解法：用一个max_reach来保存当前可以达到的最远距离，判断最远距离是否达到了终点即可

  ```java
  public boolean canJump(int[] nums) {
    int max_reach = nums[0];
    for(int i = 1; i < nums.length && max_reach >= i; i++) {
      if(max_reach < nums[i] + i) {
        max_reach = nums[i] + i;
      }
    }
    return max_reach >= nums.length - 1;
  }
  ```

### 56. Merge Intervals(Medium)

* 题意：给定区间列表，将重叠的区间合并

* 解法：先排序，之后前后区间比较来合并

   ```java
   /**
   	 * Definition for an interval.
   	 * public class Interval {
   	 *     int start;
   	 *     int end;
   	 *     Interval() { start = 0; end = 0; }
   	 *     Interval(int s, int e) { start = s; end = e; }
   	 * }
   	 */
   public List<Interval> merge(List<Interval> intervals) {
     intervals.sort((i1, i2) -> Integer.compare(i1.start, i2.start));
     for(int i = 1; i < intervals.size(); i++) {
       Interval temp = intervals.get(i);
       Interval pre = intervals.get(i - 1);
       if(pre.end >= temp.end) {
         intervals.remove(i);
         i--;
       } else if(pre.end >= temp.start) {
         pre.end = temp.end;
         intervals.set(i, pre);
         intervals.remove(i);
         i--;
       }
     }
     return intervals;
   }
   ```

### 57. Insert Interval(Hard)

* 题意：将新区间插入到一个有序无重复的区间列表里面

* 解法：首先找到新区间的位置，然后找到合并后新区间的起始点，最后加入新区间后之后的区间

   ```java
   public List<Interval> insert(List<Interval> intervals, Interval newInterval) {
     List<Interval> result = new LinkedList<>();
     int i = 0;
     while (i < intervals.size() && intervals.get(i).end < newInterval.start)
       result.add(intervals.get(i++));
     while (i < intervals.size() && intervals.get(i).start <= newInterval.end) {
       newInterval = new Interval( // we could mutate newInterval here also
         Math.min(newInterval.start, intervals.get(i).start),
         Math.max(newInterval.end, intervals.get(i).end));
       i++;
     }
     result.add(newInterval);
     while (i < intervals.size()) 
       result.add(intervals.get(i++)); 
     return result;
   }
   ```

### 58. Length of Last Word(Medium)

* 题意：给定字符串，求出最后一个单词的长度(不含空格)

* 解法：首先除去后缀空格，然后从后向前遍历并计数

  ```java
  public int lengthOfLastWord(String s) {
    int len = 0;
    int i = s.length() - 1;
    while(i >= 0 && s.charAt(i) == ' ') {
      i--;
    }
    while(i >= 0 && s.charAt(i) != ' ') {
      len++;
      i--;
    }
    return len;
  }
  ```


### 59. Spiral Matrix II(Medium)

* 题意：给定n，求出n*n的矩阵，将1到n\*n螺旋摆放

* 解法：与54题的解法相同，模拟螺旋的过程


### 60. Permutation Sequence(Medium)

* 题意：给定字符串长度n和顺序k，求出由数字组成的字典序第k小的字符串

* 解法：使用除法依次求出字符，第一个字符即第k/(n-1)!个数字（需要先k--），对k取模后计算下一个字符，依次类推；使用一个数组按照顺序存当前没用过的数字，每次求出下标后从这里取字符

  ```java
  public String getPermutation(int n, int k) {
    List<Integer> nums = new ArrayList<>();
    int[] mul = new int[n];
    int temp = 1;
    for(int i = 1; i <= n; i++) {
      nums.add(i);
      temp *= i;
      mul[n - i] = temp;
    }
  
    k--;
    String res = "";
    for(int i = 1; i < n; i++) {
      int index = k / mul[i];
      res += nums.get(index);
      nums.remove(index);
      k %= mul[i];
    }
    return res + nums.get(0);
  }
  ```

### 61. Rotate List(Medium)

* 题意：给定链表和k，将链表最后一个结点移动至链表头并执行k次

* 解法：遍历链表，求出链表长度，并将链表尾连到链表头形成循环链表，然后根据num - k来求出新的链表头即可

  ```java
  public ListNode rotateRight(ListNode head, int k) {
    if(head == null) {
      return null;
    }
    int num = 1;
    ListNode temp = head;
    while(temp.next != null) {
      num++;
      temp = temp.next;
    }
    temp.next = head;
  
    k %= num;
    int n = num - k;
    while(--n > 0) {
      head = head.next;
    }
    temp = head.next;
    head.next = null;
    return temp;
  }
  ```

### 62. Unique Paths(Medium)

* 题意：给定格子数m*n，只能向右或向下走，求出从左上角走到右下角的独特路径数

* 解法：简单的排列组合问题，步数是固定的m+n-2，要从中选出m-1步向下走，这里要注意***溢出问题***

  ```java
  public int uniquePaths(int m, int n) {
    int max = Math.max(m - 1, n - 1);
    int min = Math.min(m - 1, n - 1);
    long res = 1;
    for(int i = max + 1; i <= max + min; i++) {
      res *= i;
    }
    for(int i = 1; i <= min; i++) {
      res /= i;
    }
    return (int)res;
  }
  ```

### 63. Unique Paths II(Medium)

* 题意：给定格子二维数组，如果值为1表示该格有障碍不能走，只能向右或向下走，求出从左上角走到右下角的独特路径数

* 解法：使用动态规划求解，每个格子只能从上面或者左边来，所以就有了dp[i] [j] = dp[i-1] [j]+dp[i] [j-1]，如果有障碍则直接为0，需要初始化最上面和最左边的行列

  > 根据公式可以将dp数组简化为一维数组

  ```java
  public int uniquePathsWithObstacles(int[][] obstacleGrid) {
    int width = obstacleGrid[0].length;
    int[] dp = new int[width];
    dp[0] = 1;
    for (int[] row : obstacleGrid) {
      for (int j = 0; j < width; j++) {
        if (row[j] == 1)
          dp[j] = 0;
        else if (j > 0)
          dp[j] += dp[j - 1];
      }
    }
    return dp[width - 1];
  }
  ```

### 64. Minimum Path Sum(Medium)

* 题意：给定二维数组，只能向右或者向下走，求出从左上角到右下角中途元素和最小的路径

* 解法：和63题思路一样，使用动态规划记住到当前格子的最小和


### 65. Valid Number(Hard)

* 题意：给定字符串，判断其是否可以转化为一个小数

* 解法：使用有限状态自动机

* 解法2：先找到小数之间的字符前后关系，然后遍历一遍即可      

   ```
   We start with trimming.
   
   If we see [0-9] we reset the number flags.
   We can only see . if we didn't see e or ..
   We can only see e if we didn't see e but we did see a number. We reset numberAfterE flag.
   We can only see + and - in the beginning and after an e
   any other character break the validation.
   At the and it is only valid if there was at least 1 number and if we did see an e then a number after it as well.
   
   So basically the number should match this regular expression:
   
   [-+]?(([0-9]+(.[0-9]*)?)|.[0-9]+)(e[-+]?[0-9]+)?
   ```

#乱序版

### 901. Online Stock Span(Medium)

* 题意：编写一个股票的类，给定当前价格，求出该价格从当前到多少天之前为最高价(span)

* 解法：要比较之前的价格，所以需要使用数组来记住之前的价格，并记住之前的span，通过比较当前价格与前一天的价格来判断是否将之前的span加上来

  > 这里只需要前一天的价格，前一天值设为span1，因为从前一天到前面span1天间都可以直接跳过，所以可以使用栈来实现，从而优化空间效率

```java
class StockSpanner {
  Stack<Integer> prices, weights;

  public StockSpanner() {
    prices = new Stack();
    weights = new Stack();
  }

  public int next(int price) {
    int w = 1;
    while (!prices.isEmpty() && prices.peek() <= price) {
      prices.pop();
      w += weights.pop();
    }

    prices.push(price);
    weights.push(w);
    return w;
  }
}
```

### 911. Online Election(Medium)

* 题意：在线选举，给定times和persons数组，长度一致，对应位置i表示在times[i]时间投票给了persons[i]，求出时间t时的得票最高者，平手时最近被投票的人胜出
* 解法：遍历times数组，使用map记住当前得票数，并使用winners数组记住对应时间的胜出者，事先计算出所有赢家，之后二分搜索times得到胜者

### 226. Invert Binary Tree(Medium)

* 题意：将一颗二叉树所有左右子树交换

* 解法：使用递归，需要先判断根节点是否为null


### 393. UTF-8 Validation(Medium)

* 题意：判断UTF8格式是否有效，1-4个字节，格式如下：

  ```
     Char. number range  |        UTF-8 octet sequence
        (hexadecimal)    |              (binary)
     --------------------+---------------------------------------------
     0000 0000-0000 007F | 0xxxxxxx
     0000 0080-0000 07FF | 110xxxxx 10xxxxxx
     0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx
     0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
  ```

  给定数组，每个数字只有最低8位表示数据，判断数组所有数字是否都符合utf-8格式

* 解法：按照规则判断即可

```java
    public boolean validUtf8(int[] data) {
         int cnt = 0;
        for(int d : data){
            if(cnt == 0){
                if((d>>5) == 0b110) cnt = 1;
               else if((d>>4) == 0b1110) cnt=2;
               else if((d>>3) == 0b11110) cnt=3;
               else if((d>>7) != 0) return false;
            }else{
                if((d>>6) != 0b10) return false;
                cnt--;
            }
        }
        return cnt == 0;
    }
```

### 718. Maximum Length of Repeated Subarray(Medium)

* 题意：求出两个数组公共子数组的最大长度

* 解法：动态规划

  ```java
  class Solution {
      public int findLength(int[] A, int[] B) {
          if(A.length == 0 || B.length == 0) {
              return 0;
          }
          int[][] dp = new int[A.length + 1][B.length + 1];
          int max = 0;
          for(int i = A.length - 1; i >= 0; i--) {
              for(int j = B.length - 1; j >= 0; j--) {
                  if(A[i] == B[j]) {
                      dp[i][j] = dp[i+1][j+1] + 1;
                      if(max < dp[i][j]) {
                          max = dp[i][j];
                      }
                  } else {
                      dp[i][j] = 0;
                  }
              }
          }
          return max;
      }
  }
  ```

### 222. Count Complete Tree Nodes(Medium)

* 题意：计算树的结点个数

* 解法：使用递归


```JAVA
class Solution {
    public int countNodes(TreeNode root) {
        if(root == null) {
            return 0;
        }
        return 1 + countNodes(root.right) + countNodes(root.left);
    }
}
```

### 349. Intersection of Two Arrays(Easy)

* 题意：求出两个数组的交集

* 解法：使用两个set

  ```java
  class Solution {
      public int[] intersection(int[] nums1, int[] nums2) {
          if(nums1.length > nums2.length) {
              return intersection(nums2, nums1);
          }
          Set<Integer> set1 = new HashSet();
          Set<Integer> set2 = new HashSet();
          for(int i = 0; i < nums1.length; i++) {
              set1.add(nums1[i]);
          }
          for(int i = 0; i < nums2.length; i++) {
              if(set1.contains(nums2[i]) && !set2.contains(nums2[i])) {
                  set2.add(nums2[i]);
                  if(set1.size() == set2.size()) {
                      break;
                  }
              }
          }
          int[] res = new int[set2.size()];
          int n = 0;
          for(Integer value : set2) {
              res[n++] = value;
          }
          return res;
      }
  }
  ```

### 85. Maximal Rectangle(Hard)

- 题意：给定二维二值数组表示点，求出最大的矩形面积

- 解法：从一维数组最长公共平台得到启发，使用不同的高度，遍历每一列，如果对应行全为1则计数，并维护一个最大面积

  ```java
  class Solution {
      public int maximalRectangle(char[][] matrix) {
          if(matrix.length == 0) {
              return 0;
          }
          
          int len = matrix.length;
          int max = 0;
          while(len > 0) {
              //从i，j这个点开始看
              for(int i = 0; i + len - 1 < matrix.length; i++) {
                  int temp = 0;
                  for(int j = 0; j < matrix[0].length; j++) {
                      boolean is_rec = true;
                      //向下延伸的len个点全为1
                      for(int k = i; k < i + len; k++) {
                          if(matrix[k][j] == '0') {
                              is_rec = false;
                              break;
                          }
                      }
                      temp = is_rec ? temp + len : 0;
                      if(temp > max) {
                          max = temp;
                      }
                  }
              }
              len--;
          }
          return max;
      }
}
  ```

  * 解法2：使用动态规划
  
    ```
    [
       ["1","0","1","0","1"],
       ["1","0","1","1","1"],
       ["1","1","1","1","1"],
       ["1","0","0","1","0"]
     ]
     
    策略: 把matrix看成多个直方图, 每一行及其上方的数据都构成一个直方图, 需要考察matrix.size()个直方图
    对于每个点(row, col), 我们最后都计算以这个点上方的连续的'1'往left, right方向延申可以得到的最大的矩形的面积
    通过这种方法获取的矩形一定会把最大的矩形包含在内
    height[row][col]记录的是(row, col)这个坐标为底座的直方图柱子的高度, 如果这个点是'0', 那么高度当然是0了
    left[row][col]记录的是(row, col)这个坐标点对应的height可以延申到的最左边的位置
    right[row][col]记录的是(row, col)这个坐标点对应的height可以延申到的最右边的位置+1
    以上面的matrix为例,
    对于(row=2, col=1)这个点, left=0, right=5, height=1
    对于(row=2, col=2)这个点, left=2, right=3, height=3
    (2,2)这个点与(2,1)紧挨着,left和right却已经变化如此之大了, 这是因为left和right除了受左右两边的'1'影响, 还受到了其上方连续的'1'的制约
    由于点(2,2)上有height=3个'1', 这几个'1'的left的最大值作为当前点的left, 这几个'1'的right的最小值作为当前点的right
    因此, 实际上, 我们是要找以hight对应的这条线段往左右两边移动(只能往全是'1'的地方移动), 可以扫过的最大面积
    当hight与目标最大矩形区域的最短的height重合时, 最大矩形的面积就找到了, 如上面的例子, 就是点(2,3)或(2,4)对应的height
    
    i = 0	：1 0 1 0 1	：1 = 0 0 2 0 4
  	i = 1 ：1 0 1 1 1	：l = 0 0 2 2 4
    ```
  
    ```c++
    class Solution {
      public:
        int maximalRectangle(vector<vector<char> > &matrix) {
            if(matrix.empty()) return 0;
            const int m = matrix.size();
            const int n = matrix[0].size();
            int left[n], right[n], height[n];
            fill_n(left,n,0); fill_n(right,n,n); fill_n(height,n,0);
            int maxA = 0;
            for(int i=0; i<m; i++) {
                int cur_left=0, cur_right=n; 
                for(int j=0; j<n; j++) { // compute height (can do this from either side)
                    if(matrix[i][j]=='1') height[j]++; 
                    else height[j]=0;
                }
                for(int j=0; j<n; j++) { // compute left (from left to right)
                    if(matrix[i][j]=='1') left[j]=max(left[j],cur_left);
                    else {left[j]=0; cur_left=j+1;}
                }
                // compute right (from right to left)
                for(int j=n-1; j>=0; j--) {
                    if(matrix[i][j]=='1') right[j]=min(right[j],cur_right);
                    else {right[j]=n; cur_right=j;}    
                }
                // compute the area of rectangle (can do this from either side)
                for(int j=0; j<n; j++)
                    maxA = max(maxA,(right[j]-left[j])*height[j]);
            }
            return maxA;
        }
    };
    ```


### 84. Largest Rectangle in Histogram(Hard)

- 题意：给定数组，求出对应直方图的最大矩形面积

- 解法：对于数组中的每个高度height，求出其左右边界即可，为此需要从当前值height[i]向左右扫描，并更新边界left[i]，right[i]

  > 这里的技巧：扫描时如果判断扫描值不小于当前值height[j]，则可以使用其对应边界跳跃至j=left[j]，避免重复

  ```java
  class Solution {
      public int largestRectangleArea(int[] heights) {
          if(heights == null || heights.length == 0) {
              return 0;
          }
          int max = 0;
          int[] left = new int[heights.length];
          int[] right = new int[heights.length];
          left[0] = 0;
          right[heights.length - 1] = heights.length - 1;
          
          for(int i = 0; i < heights.length; i++) {
              int pre = i - 1;
              while(pre >= 0 && heights[i] <= heights[pre]) {
                  pre = left[pre] - 1;
              }
              left[i] = pre + 1;
          }
          for(int i = heights.length - 1; i >= 0; i--) {
              int next = i + 1;
              while(next < heights.length && heights[i] <= heights[next]) {
                  next = right[next] + 1;
              }
              right[i] = next - 1;
              
              int area = (right[i] - left[i] + 1) * heights[i];
              if(max < area) {
                  max = area;
              }
          }
          return max;
      }
  }
  ```

### 312. Burst Balloons(Hard)

- 题意：扎气球，给定数组，每个数字代表气球上的值，扎爆气球i会得到nums[i-1]*nums[i]\*num[i+1]个硬币，同时第i-1和i+1气球会变成相邻，扎爆所有气球，求出最多能得到多少硬币

- 解法：使用分治法，不考虑扎气球的顺序，遍历当前区间内要扎的气球，得到最大硬币值

  ```
  [start, ... i - 1, (i), i + 1 ... end]
  所以说整个区间可以被分解成如下部分：
  start - 1, maxCoin(start, i - 1), i, maxCoins(i + 1, end), end + 1
  ```

  ```java
  class Solution {
      public int maxCoins(int[] nums) {
          int[][] dp = new int[nums.length][nums.length];
          return maxCoins(nums, 0, nums.length - 1, dp);
      }
  
      public int maxCoins(int[] nums, int start, int end, int[][] dp) {
          if (start > end) {
              return 0;
          }
          if (dp[start][end] != 0) {
              return dp[start][end];
          }
          int max = nums[start];
          for (int i = start; i <= end; i++) {
              int val = maxCoins(nums, start, i - 1, dp) + 
                        get(nums, i) * get(nums, start - 1) * get(nums, end + 1) + 
                        maxCoins(nums, i + 1, end, dp);
  
              max = Math.max(max, val);
          }
          dp[start][end] = max;
          return max;
      }
  
      public int get(int[] nums, int i) {
          if (i == -1 || i == nums.length) {
              return 1;
          }
          return nums[i];
      }
  }
  ```

  

### 809. Expressive Words(Medium)

- 题意：给定字符串s和字符串数组w，判断s是不是由w中的字符串拉长形成的，拉长是指将1个字符变成3个及以上字符，求出匹配单词个数

- 解法：使用两个数组指针求解，分别指向s和匹配串word

  > * 如果一个letter不是extendable的说明它连续出现的次数小于3次。那么这样的letter在word中必须要和S中一样。那么我们在s和word中的pointer都++
  > * 如果是extendable，那么这个letter出现的次数肯定大于2。但是我们判断的时候并不需要算出多少的次数这么复杂。我们只要看S中看i-1，i，i+1这三个位置是不是一样 或者 i-2，i-1，i是不是一样。这里的意思是，如果这两个条件满足一个就说明至少有3个letter连续出现，那么我们只要把S的pointer++就好了。
  >   如果以上case不满足，说明这个word不是stretchy的。

  ```java
      public int expressiveWords(String S, String[] words) {
          int res = 0;
          for (String W : words) if (check(S, W)) res++;
          return res;
      }
      public boolean check(String S, String W) {
          int n = S.length(), m = W.length(), j = 0;
          for (int i = 0; i < n; i++)
              if (j < m && S.charAt(i) == W.charAt(j)) j++;
              else if (i > 1 && S.charAt(i) == S.charAt(i - 1) && S.charAt(i - 1) == S.charAt(i - 2));
              else if (0 < i && i < n - 1 && S.charAt(i - 1) == S.charAt(i) && S.charAt(i) == S.charAt(i + 1));
              else return false;
          return j == m;
      }
  ```

### 424. Longest Repeating Character Replacement(Medium)

- 题意：给定字符串，可以替换k次字符，求最长的重复字符子串

- 解法：求子串问题一般使用滑动窗口求解，这里关键在于可以替换k次字符：在窗口内使用最大频率的字符来替换！当然，如果窗口内字符替换后如果仍然有不同的字符存在，就要先缩小窗口了

  > 由于是遍历end，所以每当不符合条件时(end - start + 1 - maxCount > k)，就要将start++，因为end不能改变
  >
  > 而且由于每次end++时，一定会保证start不能回到起始位置，因为start朝前走的话一定会不符合条件

  ```java
  class Solution {
      public int characterReplacement(String s, int k) {
          int len = s.length();
          int[] count = new int[26];
          int start = 0, maxCount = 0, maxLength = 0;
          for (int end = 0; end < len; end++) {
              maxCount = Math.max(maxCount, ++count[s.charAt(end) - 'A']);
              while (end - start + 1 - maxCount > k) {
                  count[s.charAt(start) - 'A']--;
                  start++;
              }
              maxLength = Math.max(maxLength, end - start + 1);
          }
          return maxLength;
      }
  }
  ```

### 972. Equal Rational Numbers(Hard)

- 题意：给出两个字符串表示两个有理数，判断这两个有理数相不相等，注意1.0 = 0.(9)

- 解法1：思路是先将两个字符串约简，先将小数部分的尾部加入到循环部分(如果可以的话)，然后判断循环部分是否有重复，并判断循环部分是不是为0，最后判断小数部分是不是为0；接下来直接比较整数小数和循环部分，如果对上则true，否则进行进位的判断(循环部分为9)；如果有小数部分，则直接将小数部分末尾+1再比较，否则正数部分化为整型后+1再比较

  ```java
  class Solution {
      public boolean isRationalEqual(String S, String T) {
          String[] temp = S.split("[.(]");
          String i1 = temp.length > 0 ? temp[0] : "";
          String d1 = temp.length > 1 ? temp[1] : "";
          String r1 = temp.length > 2 ? temp[2].substring(0, temp[2].length() - 1) : "";
          if(r1.length() > 0) {
              for(int i = d1.length() - 1; i >= 0; i--) {
                  if(d1.charAt(i) == r1.charAt(r1.length() - 1)) {
                      d1 = d1.substring(0, i);
                      r1 = r1.charAt(r1.length() - 1) + r1.substring(0, r1.length() - 1);
                  } else {
                      break;
                  }
              }
              if(r1.length() == 4 && r1.substring(0, 2).equals(r1.substring(2))) {
                  r1 = r1.substring(2);
              }
              if(r1.length() == 3 && r1.charAt(0) == r1.charAt(1) && r1.charAt(0) == r1.charAt(2)) {
                  r1 = r1.substring(2);
              }
              if(r1.length() == 2 && r1.charAt(0) == r1.charAt(1)) {
                  r1 = r1.substring(1);
              }
              if(r1.length() == 1 && r1.charAt(0) == '0') {
                  r1 = "";
              }
          } else {
              for(int i = d1.length() - 1; i >= 0; i--) {
                      if(d1.charAt(i) == '0') {
                          d1 = d1.substring(0, i);
                      } else {
                          break;
                      }
                  }
          }
          
          temp = T.split("[.(]");
          String i2 = temp.length > 0 ? temp[0] : "";
          String d2 = temp.length > 1 ? temp[1] : "";
          String r2 = temp.length > 2 ? temp[2].substring(0, temp[2].length() - 1) : "";
          if(r2.length() > 0) {
              for(int i = d2.length() - 1; i >= 0; i--) {
                  if(d2.charAt(i) == r2.charAt(r2.length() - 1)) {
                      d2 = d2.substring(0, i);
                      r2 = r2.charAt(r2.length() - 1) + r2.substring(0, r2.length() - 1);
                  } else {
                      break;
                  }
              }
              if(r2.length() == 4 && r2.substring(0, 2).equals(r2.substring(2))) {
                  r2 = r2.substring(2);
              }
              if(r2.length() == 3 && r2.charAt(0) == r2.charAt(1) && r2.charAt(0) == r2.charAt(2)) {
                  r2 = r2.substring(2);
              }
              if(r2.length() == 2 && r2.charAt(0) == r2.charAt(1)) {
                  r2 = r2.substring(1);
              }
              if(r2.length() == 1 && r2.charAt(0) == '0') {
                  r2 = "";
              }
          } else {
              for(int i = d2.length() - 1; i >= 0; i--) {
                      if(d2.charAt(i) == '0') {
                          d2 = d2.substring(0, i);
                      } else {
                          break;
                      }
                  }
          }
          
          if(i1.equals(i2) && d1.equals(d2) && r1.equals(r2)) {
              return true;
          }
          
          if(r1.equals("9") && r2.length() == 0) {
              if(d1.length() == 0) {
                  int integer1 = Integer.valueOf(i1);
                  int integer2 = Integer.valueOf(i2);
                  return integer1 + 1 == integer2 && d2.length() == 0;
              } else {
                  d1 = d1.substring(0, d1.length() - 1) + (char)(d1.charAt(d1.length() - 1) + 1);
                  return d1.equals(d2) && i1.equals(i2);
              }
          } else if(r2.equals("9") && r1.length() == 0) {
              if(d2.length() == 0) {
                  int integer1 = Integer.valueOf(i1);
                  int integer2 = Integer.valueOf(i2);
                  return integer1 == integer2 + 1 && d1.length() == 0;
              } else {
                  d2 = d2.substring(0, d2.length() - 1) + (char)(d2.charAt(d2.length() - 1) + 1);
                  return d1.equals(d2) && i1.equals(i2);
              }
          }
          
          return false;
      }
  }
  ```

- 解法2：使用等比数列计算出数值再比较

  0.(52) = 0 + 0.52 * (1 + 0.01 + 0.0001 + ...) = 0 + 0.52 / (1 - 0.01)
  0.5(25) = 0.5 + 0.025 * (1 + 0.01 + 0.0001 + ...) = 0.5 + 0.025 / (1 - 0.01)

  ```java
  class Solution {
      private List<Double> ratios = Arrays.asList(1.0, 1.0 / 9, 1.0 / 99, 1.0 / 999, 1.0 / 9999);
  
      public boolean isRationalEqual(String S, String T) {
          return Math.abs(computeValue(S) - computeValue(T)) < 1e-9;
      }
      private double computeValue(String s) {
          if (!s.contains("(")) {
              return Double.valueOf(s);
          } else {
              double intNonRepeatingValue = Double.valueOf(s.substring(0, s.indexOf('(')));
              int nonRepeatingLength = s.indexOf('(') - s.indexOf('.') - 1;
              int repeatingLength = s.indexOf(')') - s.indexOf('(') - 1;
              int repeatingValue = Integer.parseInt(s.substring(s.indexOf('(') + 1, s.indexOf(')')));
              return intNonRepeatingValue + repeatingValue * Math.pow(0.1, nonRepeatingLength) * ratios.get(repeatingLength);
          }
      }
  }
  ```

### 491. Increasing Subsequences(Medium)

- 题意：给定数组，求出所有独特的不降序子序列(顺序固定)

- 解法：求子数组问题一般使用回溯法，这里首先要保证不降序，其次还要考虑重复元素的问题(使用HashSet来避免重复结果)

  ```java
  class Solution {
      public List<List<Integer>> findSubsequences(int[] nums) {
          List<List<Integer>> res = new ArrayList<>();
          backtrack(0, res, new ArrayList<>(), nums);
          return res;
      }
      
      void backtrack(int start, List<List<Integer>> res, List<Integer> temp, int[] nums) {
          if(temp.size() >= 2) {
              res.add(new ArrayList<>(temp));
          }
          Set<Integer> used = new HashSet();
          for(int i = start; i < nums.length; i++) {
              if(used.contains(nums[i])) {
                  continue;
              }
              if(temp.size() == 0 || temp.get(temp.size() - 1) <= nums[i]) {
                  used.add(nums[i]);
                  temp.add(nums[i]);
                  backtrack(i + 1, res, temp, nums);
                  temp.remove(temp.size() - 1);
              }
          }
      }
  }
  ```

### 970. Powerful Integers(Easy)

- 题意：给定x，y，bound，求出bound以内所有由x^i + y^j组成的数字

- 解法：观察发现由x^i + y^j可以推广到x^(i+1) + y^j和x^i + y^(j+1)，所以使用递归来解决

  ```java
  class Solution {
      public List<Integer> powerfulIntegers(int x, int y, int bound) {
          Set<Integer> res = new HashSet();
          if(bound >= 2) {
              powerfulInt(1, 1, 2, x, y, bound, res);
          }
          return new ArrayList<>(res);
      }
      
      public void powerfulInt(int m, int n, int sum, int x, int y, int bound, Set<Integer> res) {
          res.add(sum);
          int a = x * m + n;
          int b = m + y * n;
          if(a > sum && a <= bound) {
              powerfulInt(x * m, n, a, x, y, bound, res);
          }
          if(b > sum && b <= bound) {
              powerfulInt(m, y * n, b, x, y, bound, res);
          }
      }
  }
  ```

### 765. Couples Holding Hands(Hard)

- 题意：给定一个0~n-1数的一个排列，其中2i与2i+1是一对，可以将任意两个数交换，求出使得所有对相邻需要的交换次数

- 解法：贪心法，将数字对应的下标存起来，从头开始一对一对查找(没有证明)

  ```java
  class Solution {
      public int minSwapsCouples(int[] row) {
          int n = row.length;
          int[] pos = new int[n];
          for (int i = 0; i < n; i++) {
              pos[row[i]] = i;
          }
          int count = 0;
          for (int i = 0; i < n; i += 2) {
              int j = row[i] % 2 == 0 ? row[i] + 1 : row[i] - 1;
              if (row[i + 1] != j) {
                  swap(row, pos, i + 1, pos[j]);
                  count++;
              }
          }
          return count;
      }
      
      void swap(int[] row, int[] pos, int x, int y) {
          int temp = row[x];
          pos[temp] = y;
          pos[row[y]] = x;
          row[x] = row[y];
          row[y] = temp;
      }
  }
  ```

- 解法2：先考虑将n个整数通过最小次数的交换放入对应位置的问题

  将排列分解为一个一个的环，如下：

  ```
  row: 2, 3, 1, 0, 5, 4
  idx: 0, 1, 2, 3, 4, 5
  circle1: 0-->2-->1-->3-->0
  circle2: 4-->5-->4
  ```

  将一个长度为m的环要放入相应位置需要m-1次交换：将其中的任意两个元素交换，那么这个环就会被分解为2个长分别为k1和k2的环，且k1+k2=m，[可用数学归纳法证明](<https://leetcode.com/problems/couples-holding-hands/discuss/113362/JavaC%2B%2B-O(N)-solution-using-cyclic-swapping>)，放入对应位置需要k1-1+k2-1+1次交换即m-1次交换。当环中元素只有一个时，则该元素必定在其相应位置，所以需要0次交换。每次交换都会将一个环拆成两个环，实现过程如下：

  ```java
  public int miniSwapsArray(int[] row) {
      int res = 0, N = row.length;
      for (int i = 0; i < N; i++) {
        for (int j = row[i]; i != j; j = row[i]) {
          swap(row, i, j);
          res++;
        }
      }
      return res;
  }
  
  private void swap(int[] arr, int i, int j) {
      int t = arr[i];
      arr[i] = arr[j];
      arr[j] = t;
  }
  ```

  引申到该题也是一样，只是原来的条件从 i == row[i] 变为了i == ptn[pos[ptn[row[i]]]]，ptn[x]表示x的同伴对应的值，pos[x]表示值为x的下标，意思就是要让索引i对应的值的同伴的位置与i相邻

  ```java
  public int minSwapsCouples(int[] row) {
      int res = 0, N = row.length;
      int[] ptn = new int[N];    
      int[] pos = new int[N];
      
      for (int i = 0; i < N; i++) {
          ptn[i] = (i % 2 == 0 ? i + 1 : i - 1);
          pos[row[i]] = i;
      }
      
      for (int i = 0; i < N; i++) {
          for (int j = ptn[pos[ptn[row[i]]]]; i != j; j = ptn[pos[ptn[row[i]]]]) {
          swap(row, i, j);
          swap(pos, row[i], row[j]);
          res++;
        }
      }
      return res;
  }
  ```


### 501. Find Mode in Binary Search Tree(Easy)

- 题意：找出二叉搜索树中的所有众数

- 解法：借助二叉搜索树里面中序遍历的有序性，就可以当成是一个有序数组来看待，先求出数字出现的最大次数，然后求出所有众数

  ```java
  class Solution {
      public int[] findMode(TreeNode root) {
          inOrder(root);
          modeCount = maxCount;
          tempCount = 0;
          inOrder(root);
          
          int[] result = new int[res.size()];
          for(int i = 0; i < res.size(); i++) {
              result[i] = res.get(i);
          }
          return result;
      }
      
      private int maxCount = -1;
      private int modeCount = -1;
      private int tempCount = 0;
      private int tempVal = 0;
      private List<Integer> res = new ArrayList<>();
      
      void handle(int val) {
          if(val == tempVal) {
              tempCount++;
          } else {
              tempCount = 1;
              tempVal = val;
          }
          if(modeCount < 0) {
              maxCount = Math.max(maxCount, tempCount);
          } else if(modeCount == tempCount) {
              res.add(val);
          }
      }
      
      void inOrder(TreeNode root) {
          if(root == null) {
              return;
          }
          inOrder(root.left);
          handle(root.val);
          inOrder(root.right);
      }
  }
  ```


### 829. Consecutive Numbers Sum(Hard)

- 题意：给定整数N，可以用一串连续的正整数的和来表示N，有多少种这样的整数串

- 解法：数学题目，考虑等差数列的求和：*N*=(*x*+1)+(*x*+2)+⋯+(*x*+*k*), x≥0, k≥1，即2*N = k(2*x + k + 1) >= k^2，求出这样的x有多少自然数解即可

  ```java
  class Solution {
      public int consecutiveNumbersSum(int N) {
          double max = Math.sqrt(N) * Math.sqrt(2);
          int res = 0;
          for(int i = 1; i <= max; i++) {
              if((2 * N) % i == 0 && (2 * N / i - i - 1) % 2 == 0 
                 && (2 * N / i - i - 1) / 2 >= 0) {
                  res++;
              }
          }
          return res;
      }
  }
  ```

### 328. Odd Even Linked List(Medium)

- 题意：给定单链表，将链表中所有在奇数位置上的结点放到前面，偶数放到后面，并且相对位置不变，要求O(1)空间复杂度和O(n)时间复杂度

- 解法：使用4个结点：当前奇数结点temp，偶数节点开头first，偶数节点结尾last，下一个奇数结点now，搞清楚连接顺序即可

  ```java
  class Solution {
      public ListNode oddEvenList(ListNode head) {
          if(head == null || head.next == null) {
              return head;
          }
          ListNode temp = head;
          ListNode first = head.next;
          ListNode last = first;
          while(last != null && last.next != null) {
              ListNode now = last.next;
              temp.next = now;
              last.next = now.next;
              now.next = first;
              last = last.next;
              temp = now;
          }
          return head;
      }
  }
  ```

### 275. H-Index II(Medium)

- 题意：给定有序数组，求出h使得后面h个元素均不小于h而前面其他元素都小于h，要求时间复杂度O(logn)，保证一定存在这样的h

- 解法：使用二分法求解，如果当前中位数刚好等于后面的长度则为答案(保证了一定存在这样的h)，否则结果为剩下的长度

  ```java
  class Solution {
      public int hIndex(int[] citations) {
          int start = 0;
          int end = citations.length;
          while(end > start) {
              int mid = (start + end) / 2;
              int median = citations[mid];
              int len = citations.length - mid;
              if(median == len) {
                  return len;
              } else if(median < len) {
                  start = mid + 1;
              } else {
                  end = mid;
              }
          }
          return citations.length - end;
      }
  }
  ```

### 506. Relative Ranks(Easy)

- 题意：给定数组表示分数，求出每个数组元素对应的排名，保证分数的唯一性

- 解法：使用map来记住分数对应的下标，排序后直接根据排名和map来得出结果

  ```java
  class Solution {
      public String[] findRelativeRanks(int[] nums) {
          Map<Integer, Integer> index_score = new HashMap();
          String[] res = new String[nums.length];
          
          for(int i = 0; i < nums.length; i++) {
              index_score.put(nums[i], i);
          }
          Arrays.sort(nums);
          for(int i = 0; i < nums.length; i++) {
              if(i == nums.length - 1) {
                  res[index_score.get(nums[i])] = "Gold Medal";
              } else if(i == nums.length - 2) {
                  res[index_score.get(nums[i])] = "Silver Medal";
              } else if(i == nums.length - 3) {
                  res[index_score.get(nums[i])] = "Bronze Medal";
              } else {
                  res[index_score.get(nums[i])] = "" + (nums.length - i);
              }
          }
          return res;
      }
  }
  ```

### 854. K-Similar Strings(Hard)

- 题意：给定两个字符串A和B，求它们的相似度：A最少交换多少次字符可以等于B

- 解法：使用剪枝广度优先搜索可以保证***相似度的最小***，比较两个字符串，找到第一个不匹配的地方，往后查找所有可以与该位置交换的字符使得该位置与B字符串该位置的字符相等的所有串，若没有访问过，加入队列中，计数器+1，继续访问

  ```java
  class Solution {
      class Pair {
          String str;
          int count;
          public Pair(String s, int c) {
              str = s;
              count = c;
          }
      }
      public int kSimilarity(String A, String B) {
          Queue<Pair> queue = new LinkedList<>();
          Set<String> set = new HashSet();
          
          queue.offer(new Pair(A, 0));
          set.add(A);
          while(!queue.isEmpty()) {
              Pair temp = queue.poll();
              if(temp.str.equals(B)) {
                  return temp.count;
              }
              int index = 0;
              while(index < B.length()) {
                  if(temp.str.charAt(index) != B.charAt(index)) {
                      break;
                  }
                  index++;
              }
              int next = index + 1;
              while(next < B.length()) {
                  if(temp.str.charAt(next) == B.charAt(index)) {
                      String now = temp.str.substring(0, index) + temp.str.charAt(next) + temp.str.substring(index + 1, next) + temp.str.charAt(index) + temp.str.substring(next + 1);
                      if(!set.contains(now)) {
                          queue.offer(new Pair(now, temp.count + 1));
                          set.add(now);
                      }
                  }
                  next++;
              }
          }
          return -1;
      }
  }
  ```

### 209. Minimum Size Subarray Sum(Medium)

- 题意：给定数组和目标s，求出和不小于s的子数组的最小长度

- 解法：使用滑动窗口求解

  ```java
  class Solution {
      public int minSubArrayLen(int s, int[] nums) {
          if(nums.length == 0) {
              return 0;
          }
          int i = 0;
          int j = 0;
          int res = nums.length + 1;
          int sum = nums[i];
          while(i <= j && j < nums.length) {
              if(sum >= s) {
                  if(j - i + 1 < res) {
                      res = j - i + 1;
                  }
                  sum -= nums[i++];
              } else {
                  j++;
                  if(j >= nums.length) {
                      break;
                  }
                  sum += nums[j];
              }
          }
          return res <= nums.length ? res : 0;
      }
  }
  ```

### 808. Soup Servings(Medium)

- 题意：有两碗汤A和B，各有Nml，有四种取汤方案：A25B75，A50B50，A75B25，A100B0，四种方案概率一样0.25，求A先取完的概率+AB同时取完的概率的一半

- 解法：使用动态规划

  > 1.使用动态规划，按照25ml来量取，可以先都除以25，这样可以减小数组大小
  >
  > 2.当由于方案不对称性，所以N达到一定大小时，结果可以近似为1，这里求出来是5000，所以数组大小只需200*200即可

  ```java
  class Solution {
      public double soupServings(int N) {
          return N >= 5000 ?  1.0 : f((N + 24) / 25, (N + 24) / 25);
      }
  
      double[][] memo = new double[200][200];
      public double f(int a, int b) {
          if (a <= 0 && b <= 0) return 0.5;
          if (a <= 0) return 1;
          if (b <= 0) return 0;
          if (memo[a][b] > 0) return memo[a][b];
          memo[a][b] = 0.25 * (f(a - 4, b) + f(a - 3, b - 1) + f(a - 2, b - 2) + f(a - 1, b - 3));
          return memo[a][b];
      }
  }
  ```

### 948. Bag of Tokens(Medium)

- 题意：有P点能量和一个tokens数组，可以使用一点积分来兑换一个token的能量，也可以用一个token的能量来兑换一个积分，求最多能得到多少积分

- 解法：对tokens数组排序后，使用滑动窗口求解，从小的token开始兑换积分，能量不够再去大的token兑换能量，并维护一个最大积分

  ```java
  class Solution {
      public int bagOfTokensScore(int[] tokens, int P) {
          Arrays.sort(tokens);
          int points = 0;
          int max = 0;
          int start = 0;
          int end = tokens.length - 1;
          while(start <= end) {
              if(P >= tokens[start]) {
                  P -= tokens[start++];
                  points++;
                  max = Math.max(max, points);
              } else if(points > 0) {
                  points--;
                  P += tokens[end--];
              } else {
                  break;
              }
          }
          return max;
      }
  }
  ```

### 503. Next Greater Element II(Medium)

- 题意：给定数组，首尾元素连接(相等)，求出数组所有元素的下一个比它大的元素

- 解法：使用栈来维护当前无法求出的元素下标，当遇到足够大的元素，则一起判断来出栈

  ```java
  class Solution {
      public int[] nextGreaterElements(int[] A) {
          int n = A.length, res[] = new int[n];
          Arrays.fill(res, -1);
          Stack<Integer> stack = new Stack<>();
          for(int i = 0; i < n * 2; i++) {
              while(!stack.isEmpty() && A[stack.peek()] < A[i % n])
                  res[stack.pop()] = A[i % n];
              stack.push(i % n);
          }
          return res;
      }
  }
  ```

### 994. Rotting Oranges(Easy)

- 题意：给定二维数组，值为0表示空1表示好橘子2表示坏橘子，每分钟坏橘子会让四周的好橘子变坏，多少分钟橘子全部变坏

- 解法：使用层次遍历的思想

  ```java
  class Solution {
      public class Cell{
          int x;
          int y;
          int val;
          public Cell(int a, int b, int c) {
              x = a;
              y = b;
              val = c;
          }
      }
      
      public int orangesRotting(int[][] grid) {
          Queue<Cell> queue = new LinkedList<>();
          int sum = 0;
          for(int i = 0; i < grid.length; i++) {
              for(int j = 0; j < grid[i].length; j++) {
                  if(grid[i][j] == 2) {
                      queue.offer(new Cell(i, j, grid[i][j]));
                  } else if(grid[i][j] == 1) {
                      sum++;
                  }
              }
          }
          int time = 0;
          int len = 0;
          int next_len = queue.size();
          while(sum > 0 && !queue.isEmpty()) {
              if(len == 0) {
                  len = next_len;
                  time++;
                  next_len = 0;
              }
              Cell rotten = queue.poll();
              Cell cell1 = get(rotten.x - 1, rotten.y, grid);
              Cell cell2 = get(rotten.x, rotten.y - 1, grid);
              Cell cell3 = get(rotten.x + 1, rotten.y, grid);
              Cell cell4 = get(rotten.x, rotten.y + 1, grid);
              if(cell1 != null && cell1.val == 1) {
                  queue.offer(cell1);
                  grid[cell1.x][cell1.y] = 2;
                  sum--;
                  next_len++;
              }
              if(cell2 != null && cell2.val == 1) {
                  queue.offer(cell2);
                  grid[cell2.x][cell2.y] = 2;
                  sum--;
                  next_len++;
              }
              if(cell3 != null && cell3.val == 1) {
                  queue.offer(cell3);
                  grid[cell3.x][cell3.y] = 2;
                  sum--;
                  next_len++;
              }
              if(cell4 != null && cell4.val == 1) {
                  queue.offer(cell4);
                  grid[cell4.x][cell4.y] = 2;
                  sum--;
                  next_len++;
              }
              len--;
          }
          return sum > 0 ? -1 : time;
      }
      
      private Cell get(int x, int y, int[][] grid) {
          if(x >= 0 && x < grid.length) {
              if(y >= 0 && y < grid[x].length) {
                  return new Cell(x, y, grid[x][y]);
              }
          }
          return null;
      }
  }
  ```

### 394. Decode String(Medium)

- 题意：给定加密字符串，形式为"3[af2[32]c]a4[fa]f"，对其解密

- 解法：使用栈来实现

  ```java
  class Solution {
      public class Encode {
          int num;
          String str;
          public Encode(int n, String s) {
              num = n;
              str = s;
          }
          
          public void addStr(String s) {
              str += s;
          }
          
          public String decode() {
              StringBuilder sb = new StringBuilder();
              for(int i = 0; i < num; i++) {
                  sb.append(str);
              }
              return sb.toString();
          }
      }
      public String decodeString(String s) {
          Stack<Encode> stack = new Stack<>();
          stack.push(new Encode(1, ""));
          for(int i = 0; i < s.length(); i++) {
              if(s.charAt(i) > '0' && s.charAt(i) <= '9') {
                  int temp = s.charAt(i) - '0';
                  while(s.charAt(++i) >= '0' && s.charAt(i) <= '9') {
                      temp = temp * 10 + s.charAt(i) - '0';
                  }
                  stack.push(new Encode(temp, ""));
              } else if(s.charAt(i) == ']') {
                  Encode top = stack.pop();
                  stack.peek().addStr(top.decode());
              } else {
                  stack.peek().addStr("" + s.charAt(i));
              }
          }
          return stack.peek().decode();
      }
  }
  ```

### 494. Target Sum(Medium)

- 题意：给定非负数组nums和目标sum，数组中每个元素可以使用+或者-，求有多少种方式得到目标

- 解法：题意即将数组拆分为正负两个集合P和N，计算公式如下

  > ```
  > sum(P) - sum(N) = target
  > sum(P) + sum(N) + sum(P) - sum(N) = target + sum(P) + sum(N)
  > 2 * sum(P) = target + sum(nums)
  > ```

  接下来就是一个简单的凑出目标和的求子集问题，使用01背包可以解决

  ```java
  class Solution {
      public int findTargetSumWays(int[] nums, int s) {
          int sum = 0;
          for (int n : nums)
              sum += n;
          return sum < s || (s + sum) % 2 > 0 ? 0 : subsetSum(nums, (s + sum) >>> 1); 
      }   
  
      public int subsetSum(int[] nums, int s) {
          int[][] dp = new int[nums.length + 1][s + 1];
          dp[0][0] = 1;
          for (int i = 1; i < nums.length + 1; i++) {
              dp[i][0] = dp[i-1][0] * (nums[i - 1] == 0 ? 2 : 1);
          }
          for (int i = 1; i < nums.length + 1; i++) {
              for (int j = 1; j < s + 1; j++) {
                  dp[i][j] = dp[i - 1][j];
                  if (j >= nums[i - 1]) {
                      dp[i][j] += dp[i - 1][j - nums[i - 1]];
                  }
              }
          }
          return dp[nums.length][s];
      } 
  }
  ```

### 282. Expression Add Operators(Hard)

- 题意：给定一串数字，数字之间可以相邻或添加+或添加-或添加*，通过运算求出达到目标值得所有方式

- 解法：使用回溯法求解

  ```java
  class Solution {
      public List<String> addOperators(String num, int target) {
          List<String> res = new ArrayList<>();
          if(num == null || num.length() == 0) {
              return res;
          }
          backtrack(res, "", num, target, 0, 0, 0);
          return res;
      }
      
      private void backtrack(List<String> rst, String path, String num, int target, int pos, long eval, long multed){
          if(pos == num.length()){
              if(target == eval)
                  rst.add(path);
              return;
          }
          for(int i = pos; i < num.length(); i++){
              if(i != pos && num.charAt(pos) == '0') {
                  break;
              }
              long cur = Long.parseLong(num.substring(pos, i + 1));
              if(pos == 0){
                  backtrack(rst, path + cur, num, target, i + 1, cur, cur);
              }
              else{
                  backtrack(rst, path + "+" + cur, num, target, i + 1, eval + cur , cur);
                  
                  backtrack(rst, path + "-" + cur, num, target, i + 1, eval -cur, -cur);
                  
                  backtrack(rst, path + "*" + cur, num, target, i + 1, eval - multed + multed * cur, multed * cur );
              }
          }
      }
  }
  ```


### 1011. Capacity To Ship Packages Within D Days(Medium)

- 题意：给定物品重量数组weights，和装载天数D，要在D天内按顺序装入所有物品，求最小的每日装入重量

- 解法：将一个数组划分为D个子数组，所以可以先求sum/D和max，这就是结果的下界，上界为sum，所以可以用***二分搜索***来查找结果

  ```java
  class Solution {
      public int shipWithinDays(int[] weights, int D) {
          if(weights == null || weights.length == 0) {
              return 0;
          }
          int right = 0;
          int left = 0;
          for(int i = 0; i < weights.length; i++) {
              right += weights[i];
              left = Math.max(left, weights[i]);
          }
          left = Math.max(left, right / D);
          while(left <= right) {
              int temp = (left + right) / 2;
              int i = 0;
              int d = D;
              int temp_sum = 0;
              while(d > 0 && i < weights.length) {
                  temp_sum += weights[i];
                  if(temp_sum > temp) {
                      d--;
                      temp_sum = 0;
                  } else {
                      i++;
                  }
              }
              if(i == weights.length) {
                  right = temp - 1;
              } else {
                  left = temp + 1;
              }
          }
          return left;
      }
  }
  ```

### 117. Populating Next Right Pointers in Each Node II(Medium)

- 题意：给定二叉树，为每个节点找到右边第一个兄弟节点

- 解法：使用层次遍历，这里不需要用到队列，因为不像数组二叉树本身就有层次结构，只需记住下一层的头结点和下一层的前一节点

  ![Alt text](https://assets.leetcode.com/uploads/2019/02/15/117_sample.png)

  ```java
  class Solution {
      public Node connect(Node root) {
          if(root == null) {
              return root;
          }
          Node temp = root;
          Node pre = null;
          Node head = null;
          while(temp != null) {
              while(temp != null) {
                  if(temp.left != null) {
                      if(pre != null) {
                          pre.next = temp.left;
                      } else {
                          head = temp.left;
                      }
                      pre = temp.left;
                  }
                  if(temp.right != null) {
                      if(pre != null) {
                          pre.next = temp.right;
                      } else {
                          head = temp.right;
                      }
                      pre = temp.right;
                  }
                  temp = temp.next;
              }
              temp = head;
              pre = null;
              head = null;
          }
          return root;
      }
  }
  ```

### 400. Nth Digit(Easy)

- 题意：在正整数序列中找到第n个数字(单个)

- 解法：先求出数字的位数，然后求出所在数字的值，最后求出该数字

  ```java
  class Solution {
      public int findNthDigit(int n) {
          int i = 1;
          int temp = 1;
          long limit = 9 * temp * i;
          while(n > limit) {
              n -= limit;
              i++;
              temp *= 10;
              limit = limit * 10 + temp * 9;
          }
          int num = (n - 1) / i + temp;
          int remain = n % i;
          remain = remain > 0 ? (i - remain) : remain;
          while(remain-- > 0) {
              num /= 10;
          }
          return num % 10;
      }
  }
  ```

### 611. Valid Triangle Number(Medium)

- 题意：给定数组，表示边长，求出其中能组成的三角形个数(可重复)

- 解法：3Sum的变式，需要先排序

  ```java
  class Solution {
      public int triangleNumber(int[] nums) {
          Arrays.sort(nums);
          int res = 0;
          for(int i = nums.length - 1; i >= 2; i--) {
              int j = 0;
              int k = i - 1;
              while(j < k) {
                  if(nums[j] + nums[k] > nums[i]) {
                      res += k - j;
                      k--;
                  } else {
                      j++;
                  }
              }
          }
          return res;
      }
  }
  ```

### 417. Pacific Atlantic Water Flow(Medium)

- 题意：给定二维数组表示高度，水可以从高的地方流向低的或者平的地方，求出所有能流出至左上方和右下方的地方

- 解法：使用广度优先遍历(不能使用动态规划，因为水能流向四个方向，可能会有循环)

  ```java
  public class Solution {
      int[][]dir = new int[][]{{1,0},{-1,0},{0,1},{0,-1}};
      public List<int[]> pacificAtlantic(int[][] matrix) {
          List<int[]> res = new LinkedList<>();
          if(matrix == null || matrix.length == 0 || matrix[0].length == 0){
              return res;
          }
          int n = matrix.length, m = matrix[0].length;
          //One visited map for each ocean
          boolean[][] pacific = new boolean[n][m];
          boolean[][] atlantic = new boolean[n][m];
          Queue<int[]> pQueue = new LinkedList<>();
          Queue<int[]> aQueue = new LinkedList<>();
          for(int i=0; i<n; i++){ //Vertical border
              pQueue.offer(new int[]{i, 0});
              aQueue.offer(new int[]{i, m-1});
              pacific[i][0] = true;
              atlantic[i][m-1] = true;
          }
          for(int i=0; i<m; i++){ //Horizontal border
              pQueue.offer(new int[]{0, i});
              aQueue.offer(new int[]{n-1, i});
              pacific[0][i] = true;
              atlantic[n-1][i] = true;
          }
          bfs(matrix, pQueue, pacific);
          bfs(matrix, aQueue, atlantic);
          for(int i=0; i<n; i++){
              for(int j=0; j<m; j++){
                  if(pacific[i][j] && atlantic[i][j])
                      res.add(new int[]{i,j});
              }
          }
          return res;
      }
      public void bfs(int[][]matrix, Queue<int[]> queue, boolean[][]visited){
          int n = matrix.length, m = matrix[0].length;
          while(!queue.isEmpty()){
              int[] cur = queue.poll();
              for(int[] d:dir){
                  int x = cur[0]+d[0];
                  int y = cur[1]+d[1];
                  if(x<0 || x>=n || y<0 || y>=m || visited[x][y] || matrix[x][y] < matrix[cur[0]][cur[1]]){
                      continue;
                  }
                  visited[x][y] = true;
                  queue.offer(new int[]{x, y});
              } 
          }
      }
  }
  ```

### 143. Reorder List(Medium)

- 题意：给定链表*L*: *L*0→*L*1→…→*L*n，重排为*L*0→*Ln*→*L1→*Ln*-1→*L*2→*L*n*-2→…

- 解法：先找到链表中间，然后将后面的反序，然后再插入前面

  ```java
  class Solution {
      public void reorderList(ListNode head) {
          if(head == null) {
              return;
          }
          int num = 0;
          ListNode temp = head;
          while(temp != null) {
              num++;
              temp = temp.next;
          }
          int mid = (num / 2) + 1;
          temp = head;
          while(--mid > 0) {
              temp = temp.next;
          }
          ListNode first = temp;
          ListNode last = temp.next;
          while(last != null && last.next != null) {
              ListNode now = last.next;
              last.next = now.next;
              now.next = first.next;
              first.next = now;
              now = last.next;
          }
          temp = head;
          while(first.next != null) {
              ListNode now = temp.next;
              ListNode x = first.next;
              first.next = x.next;
              temp.next = x;
              x.next = now;
              temp = now;
          }
      }
  }
  ```

### 1016. Binary String With Substrings Representing 1 To N(Medium)

- 题意：给定二进制字符串S和数字N，判断字符串是不是包含所有1~N的子串
- 解法：暴力法，但是只需要判断从N/2~N即可

### 878. Nth Magical Number(Hard)

- 题意：给定A，B，N，求出第N个能被A或者B整除的数字

- 解法：先求出A和B的gcd和lcm，然后推导出了一个不等式，并由此进行二分搜索

  ```java
  class Solution {
      public int nthMagicalNumber(int N, int A, int B) {
          int MOD = 1_000_000_007;
          int LCM = A / gcd(A, B) * B;
  
          long low = 0;
          long high = (long) 1e15;
          while (low < high) {
              long mi = low + (high - low) / 2;
              // If there are not enough magic numbers below mi...
              if (mi / A + mi / B - mi / LCM < N)
                  low = mi + 1;
              else
                  high = mi;
          }
  
          return (int) (low % MOD);
      }
  
      public int gcd(int x, int y) {
          if (x == 0) return y;
          return gcd(y % x, x);
      }
  }
  ```

### 526. Beautiful Arrangement(Medium)

- 题意：给定数组元素个数N，这N个数是1~N的一个排列，并且每个数要么是下标的倍数，要么是下标的因子，求这种排列的可能个数

- 解法：使用深度优先搜索并剪枝，这里只需要使用一个used布尔数组来记录每个数字的使用情况即可，不需要记住当前的路径，并判断当前元素是否符合条件

  ```java
  class Solution {
      public int countArrangement(int N) {
          dfs(0, N, new boolean[N]);
          return res;
      }
      
      int res = 0;
      private void dfs(int size, int len, boolean[] used) {
          if(size == len) {
              res++;
              return;
          }
          for(int i = 1; i <= len; i++) {
              if(!used[i-1] && (i % (size + 1) == 0 || (size + 1) % i == 0)) {
                  used[i-1] = true;
                  dfs(size + 1, len, used);
                  used[i-1] = false;
              }
           }
      }
  }
  ```

- 解法2：在上面解法的基础上，可以不使用used数组来记录，而是记住数组的情况，通过交换数组元素的位置来保证没有重复

  ```java
  class Solution {
      public int countArrangement(int N) {
          int[] nums = new int[N];
          for(int i = 0; i < N; i++) {
              nums[i] = i + 1;
          }
          dfs(nums, 0);
          return res;
      }
      
      int res = 0;
      private void dfs(int[] nums, int start) {
          int len = nums.length;
          if(start == len) {
              res++;
              return;
          }
          
          for(int i = start; i < len; i++) {
              swap(nums, start, i);
              if(nums[start] % (start + 1) == 0 || (start + 1) % nums[start] == 0) {
                  dfs(nums, start + 1);
              }
              swap(nums, start, i);
           }
      }
      
      private void swap(int[] nums, int i, int j) {
          int temp = nums[j];
          nums[j] = nums[i];
          nums[i] = temp;
      }
  }
  ```

### 899. Orderly Queue(Hard)

- 题意：给定字符串，可以多次将前K个字符中任取一个放到最后，问得到的所有字符串中字典序最小的是哪个

- 解法：当K = 2时即相邻的两个字符可以交换位置，这样的话意味着所有的排列都可以得到，所以K  > 1时即求最小字典序字符串

  ```java
  class Solution {
      public String orderlyQueue(String S, int K) {
          if(K > 1) {
              char[] temp = S.toCharArray();
              Arrays.sort(temp);
              return new String(temp);
          }
          String res = S;
          for(int i = 0; i < S.length(); i++) {
              String now = S.substring(i, S.length()) + S.substring(0, i);
              if(now.compareTo(res) < 0) {
                  res = now;
              }
          }
          return res;
      }
  }
  ```

### 415. Add Strings(Easy)

- 题意：给定两个字符串表示数字，求数字之和

- 解法：字符串的加法，注意使用StringBuilder才有reverse方法

  ```java
  public class Solution {
      public String addStrings(String num1, String num2) {
          StringBuilder sb = new StringBuilder();
          int carry = 0;
          for(int i = num1.length() - 1, j = num2.length() - 1; i >= 0 || j >= 0 || carry == 1; i--, j--){
              int x = i < 0 ? 0 : num1.charAt(i) - '0';
              int y = j < 0 ? 0 : num2.charAt(j) - '0';
              sb.append((x + y + carry) % 10);
              carry = (x + y + carry) / 10;
          }
          return sb.reverse().toString();
      }
  }
  ```

### 105. Construct Binary Tree from Preorder and Inorder Traversal(Medium)

- 题意：给定二叉树的前序和中序遍历序列（无重复元素），构建二叉树

- 解法：根据前序和中序特性，前序第一个为根节点，中序在该结点左边的为左子树，右边为右子树，再继续寻找左右子树的根节点

  ```java
  class Solution {
      public TreeNode buildTree(int[] preorder, int[] inorder) {
          return buildSubTree(preorder, inorder, 0, 0, preorder.length);
      }
      
      private TreeNode buildSubTree(int[] preorder, int[] inorder, int root, int start, int end) {
          if(root < preorder.length && start < end) {
              TreeNode node = new TreeNode(preorder[root]);
              int index = findNode(inorder, preorder[root], start, end);
              node.left = buildSubTree(preorder, inorder, root + 1, start, index);
              node.right = buildSubTree(preorder, inorder, root + index - start + 1, index + 1, end);
              return node;
          }
          return null;
      }
      
      private int findNode(int[] array, int num, int start, int end) {
          for(int i = start; i < end; i++) {
              if(array[i] == num) {
                  return i;
              }
          }
          return 0;
      }
  }
  ```

### 962. Maximum Width Ramp(Medium)

- 题意：给定数组A，求出i < j 且 A[i] <= A[j] 的最大宽度 j - i 

- 解法1：先使用两个map分别记住数组元素的最大和最小索引，然后将数组排序，维护一个最小索引，对每个元素计算宽度，得到最大宽度，复杂度为O(nlog n)

  ```java
  class Solution {
      public int maxWidthRamp(int[] A) {
          HashMap<Integer, Integer> min_index_map = new HashMap();
          HashMap<Integer, Integer> max_index_map = new HashMap();
          for(int i = 0; i < A.length; i++) {
              if(!min_index_map.containsKey(A[i])) {
                  min_index_map.put(A[i], i);
                  max_index_map.put(A[i], i);
              } else {
                  max_index_map.put(A[i], i);
              }
          }
          Arrays.sort(A);
          int min_index = A.length;
          int max_width = 0;
          for(int i = 0; i < A.length; i++) {
              if(min_index > min_index_map.get(A[i])) {
                  min_index = min_index_map.get(A[i]);
              } else {
                  int width = max_index_map.get(A[i]) - min_index;
                  max_width = Math.max(width, max_width);
              }
          }
          return max_width;
      }
  }
  ```

- 解法2：维护一个栈，保存数组的递减序列，每次都从栈中二分搜索，更新最大值，复杂度为O(nlog n)

  ```java
   public int maxWidthRamp(int[] A) {
     List<Integer> s = new ArrayList<>();
     int res = 0, n = A.length;
     for (int i = 0; i < n; ++i) {
       if (s.size() == 0 || A[i] < A[s.get(s.size() - 1)]) {
         s.add(i);
       } else {
         int left = 0, right = s.size() - 1, mid = 0;
         while (left < right) {
           mid = (left + right) / 2;
           if (A[s.get(mid)] > A[i]) {
             left = mid + 1;
           } else {
             right = mid;
           }
         }
         res = Math.max(res, i - s.get(left));
       }
     }
     return res;
   }
  ```

- 解法3：由于维护了一个递减栈，这样就记住了所有可能的最小索引i，接下来只需要找到最大索引j即可，从后向前遍历，如果满足条件，则弹栈并更新最大宽度，贪心算法，复杂度为O(n)

  ```java
  public int maxWidthRamp(int[] A) {
    Stack<Integer> s = new Stack<>();
    int res = 0, n = A.length;
    for (int i = 0; i < n; ++i)
      if (s.empty() || A[s.peek()] > A[i])
        s.add(i);
    for (int i = n - 1; i > res; --i)
      while (!s.empty() && A[s.peek()] <= A[i])
        res = Math.max(res, i - s.pop());
    return res;
  }
  ```

* 解法4：维护两个数组，分别表示该元素左边的最小值和右边的最大值，然后用两个指针分别表示最小值和最大值从最左边走到最右边，途中更新最大间隔

  ```java
  class Solution {
      public int maxWidthRamp(int[] A) {
          int n=A.length;
          if(n==1){
              return 0;
          }
          int[] lmin=new int[n];
          int[] rmax=new int[n];
          lmin[0]=A[0];
          for(int i=1;i<n;i++){
              lmin[i]=Math.min(lmin[i-1],A[i]);
          }
          rmax[n-1]=A[n-1];
          for(int i=n-2;i>=0;i--){
              rmax[i]=Math.max(rmax[i+1],A[i]);
          }
          int sp=0;
          int ep=0;
          int max=0;
          while(ep<n){
              if(lmin[sp]<=rmax[ep]){
                  // Since, it is valid.We update the length.
                  max=Math.max(max,ep-sp);
                  ep++;
              }else{
                  sp++;
              }
          }
          return max;
      }
  }
  ```

### 524. Longest Word in Dictionary through Deleting(Medium)

- 题意：给定字符串s和字符串数组d，找到d中最长中字典序最小的能通过s删掉一些字符得到的字符串

- 解法：逐个比较，判断d中每个字符串是否可以由s构造出来，找到需要的那个

  ```java
  class Solution {
      public String findLongestWord(String s, List<String> d) {
          String res = "";
          for(String str: d) {
              if(isFormedString(s, str) && (str.length() > res.length() || (str.length() == res.length() && res.compareTo(str) > 0))) {
                  res = str;
              }
          }
          return res;
      }
      
      private boolean isFormedString(String s, String d) {
          int i = 0;
          int j = 0;
          while(i < s.length() && j < d.length()) {
              if(s.charAt(i) == d.charAt(j)) {
                  i++;
                  j++;
              } else {
                  i++;
              }
          }
          return j == d.length();
      }
  }
  ```

### 200. Number of Islands(Medium)

- 题意：给定字符二维数组，1表示陆地，0表示海洋，求有多少块陆地

- 解法：使用图的遍历算法即可，使用dfs可以用递归，代码量更少

  ```java
  public int numIslands(char[][] grid) {
      int count=0;
      for(int i=0;i<grid.length;i++)
          for(int j=0;j<grid[0].length;j++){
              if(grid[i][j]=='1'){
                  dfsFill(grid,i,j);
                  count++;
              }
          }
      return count;
  }
  private void dfsFill(char[][] grid,int i, int j){
      if(i>=0 && j>=0 && i<grid.length && j<grid[0].length&&grid[i][j]=='1'){
          grid[i][j]='0';
          dfsFill(grid, i + 1, j);
          dfsFill(grid, i - 1, j);
          dfsFill(grid, i, j + 1);
          dfsFill(grid, i, j - 1);
      }
  }
  ```

### 738. Monotone Increasing Digits(Medium)

- 题意：给定N，找到不大于N的所有数中数字按照非减顺序排列的最大数字

- 解法：拿到数字后观察规律，发现如果找到了一个逆序对last，temp后，last之后所有的数字都会置为9，last-1，然后再重新向前比较，直到满足条件，为此需要计算last和temp，通过计算位数来确定

  > 使用字符串数组操作可能更加直观String.valueOf(N).toCharArray()，但是牺牲了空间复杂度

  ```java
  class Solution {
      public int monotoneIncreasingDigits(int N) {
          int n = N;
          int num = 0;
          int x = 1;
          while(n > 0) {
              n /= 10;
              x *= 10;
              num++;
          }
          if(num == 1) {
              return N;
          }
          x /= 100;
          n = N;
          int last = (n / (10 * x)) % 10;
          int temp = (n / x) % 10;
          while(last <= temp && x > 1) {
              x /= 10;
              last = (n / (10 * x)) % 10;
              temp = (n / x) % 10;
          }
          while(last > temp) {
              x *= 10;
              n = n / x * x - 1;
              last = (n / (10 * x)) % 10;
              temp = (n / x) % 10;
          }
          return n;
      }
  }
  ```

### 947. Most Stones Removed with Same Row or Column(Medium)

- 题意：给定有石头的坐标位置stones，每次可以移除一个在同一行或者同一列有其他石头的石头，最多能移除多少个石头

- 解法1：使用深度搜索，深度搜索中的结点数决定了移除石头的数目，这是因为深度搜索使用visited数组确保不会重复，按照深度也保证了问题的最优解

  > 这里实际上是数岛屿问题，相当于有几个数岛屿，每个岛屿最后会剩下最后一块石头，所有结果就是石头数-岛屿数

  ```java
  class Solution {
      // Ans = # of stones – # of islands
      public int removeStones(int[][] stones) {
          Set<int[]> visited = new HashSet();
          int numOfIslands = 0;
          for (int[] s1:stones){
              if (!visited.contains(s1)){
                 dfs(s1, visited, stones); 
                 numOfIslands++;
              }
          }
          return stones.length - numOfIslands;
      }
      
      private void dfs(int[] s1, Set<int[]> visited, int[][] stones){
          visited.add(s1);
          for (int[] s2: stones){
              if (!visited.contains(s2)){
  				// stone with same row or column. group them into island
                  if (s1[0] == s2[0] || s1[1] == s2[1])
                      dfs(s2, visited, stones);
              }
          }
      }
  }
  ```

- 解法2：对于这种连通图问题，一般使用并查集，首先每个石头都是一个集合，然后将石头集合按照规则进行合并，最后剩下的几个集合即为岛屿

  > 可以将行和列进行分别处理，将列数重新映射

  ```java
  class Solution {
      //集合的组下标映射
      Map<Integer, Integer> f = new HashMap<>();
      int islands = 0;
  
      public int removeStones(int[][] stones) {
          //对每个石头都进行并查集的合并
          for (int i = 0; i < stones.length; ++i)
              union(stones[i][0], ~stones[i][1]);
          return stones.length - islands;
      }
  
      //根据映射表找到x的组下标
      public int find(int x) {
          //如果是第一次加入则作为一个新集合
          if (f.putIfAbsent(x, x) == null)
              islands++;
          //由于x组下标在union函数中可能进行了更新，所以需要再递归更新组下标
          //需要保证不会陷入死循环
          if (x != f.get(x))
              f.put(x, find(f.get(x)));
          //返回最新的x组下标
          return f.get(x);
      }
  
      //给定横纵坐标，合并横纵坐标对应的组
      public void union(int x, int y) {
          //首先找到横纵坐标对应的组下标
          x = find(x);
          y = find(y);
          //如果不是同一组则集合数-1
          //并且将横坐标组下标映射到纵坐标组下标
          if (x != y) {
              f.put(x, y);
              islands--;
          }
      }
  }
  ```

### 189. Gray Code(Medium)

- 题意：给定二进制位数n，求出格雷码(相邻元素只有一位不同)

- 解法：使用二进制或运算，借助元素的顺序性，可以将上一次求得的数倒序再用一遍，同时只修改当前最高位的元素，从低到高求解

  ```java
  class Solution {
      public List<Integer> grayCode(int n) {
          List<Integer> res=new ArrayList<Integer>();
          res.add(0);
          for(int i = 0; i < n; i++){
              for(int k = res.size() - 1; k >= 0; k--) {
                  res.add(res.get(k) | 1 << i);
              }
          }
          return res;
      }
  }
  ```

### 953. Verifying an Alien Dictionary(Easy)

- 题意：外星文字使用小写字母但是字母顺序不同，给定字符串数组，判断其是否按照字典序升序
- 解法：构造新的字母大小映射关系，重新实现比较算法

### 279. Perfect Squares(Medium)

- 题意：每个数字都可以只由平方数相加组成，求出最少要多少个平方数才能得到的给定n

- 解法：使用动态规划求解

  ```java
  class Solution {
      public int numSquares(int n) {
          int[] dp = new int[n + 1];
          return getNum(n, dp);
      }
      
      private int getNum(int n, int[] dp) {
          if(dp[n] > 0) {
              return dp[n];
          }
          int max_square = (int)Math.sqrt(n);
          if(max_square * max_square == n) {
              dp[n] = 1;
              return 1;
          }
          int min = n;
          for(int i = max_square; i > 0; i--) {
              int res = 1;
              int temp = n - i * i;
              res += getNum(temp, dp);
              min = Math.min(res, min);
          }
          dp[n] = min;
          return min;
      }
  }
  ```

### 215. Kth Largest Element in an Array(Medium)

- 题意：求出数组中第k大的元素

- 解法：快排的基本思想，从大到小排序，每次都能确认划分的前面元素一定比后面元素大，按照个数再选择对哪边排序

- 也可以使用堆排序来做，k个元素的大根堆

  ```java
  class Solution {
      public int findKthLargest(int[] nums, int k) {
          int low = 0;
          int high = nums.length - 1;
          while(true) {
              int mid = partition(nums, low, high);
              if(mid + 1 == k) {
                  return nums[mid];
              } else if(mid + 1 > k) {
                  high = mid - 1;
              } else {
                  low = mid + 1;
              }
          }
      }
      
      private int partition(int[] a, int low, int high) {
          int base = a[low];
          while(low < high) {
              while(low < high && a[high] <= base) high--;
              a[low] = a[high];
              while(low < high && a[low] >= base) low++;
              a[high] = a[low];
          }
          a[low] = base;
          return low;
      }
  }
  ```

### 888. Fair Candy Swap(Easy)

- 题意：给定两个数组，交换一次元素使得两数组和相等(保证有解)

- 解法：先分别求出两个数组的元素和，根据其中的差别求出相差n，然后就转化为求出两个相差n的元素，可以使用set来求

  ```java
  class Solution {
      public int[] fairCandySwap(int[] A, int[] B) {
          int sum_a = 0;
          for(int a: A) {
              sum_a += a;
          }
          int sum_b = 0;
          HashSet<Integer> set = new HashSet();
          for(int b: B) {
              sum_b += b;
              set.add(b);
          }
          int n = (sum_a + sum_b) / 2 - sum_a;
          for(int a: A) {
              if(set.contains(a + n)) {
                  return new int[]{a, a + n};
              }
          }
          return new int[2];
      }
  }
  ```

### 445. Add Two Numbers II(Medium)

- 题意：给定两个链表，按照十进制从高位到低位存储数字，实现链表的加法
- 解法：通过链表反转可以保证低位先加，然后再实现链表加法，最后将结果反转回来

### 629. K Inverse Pairs Array(Hard)

- 题意：给定n和k，求出前n个正整数所有排列中刚好有k个逆序对的排列数

- 解法：动态规划，先只考虑第n大正整数，如果放在最后则提供了0个逆序对，放在倒数第二个位置则提供了一个逆序对，以此类推，放在第一个位置提供了n-1个逆序对，所以剩下的逆序对要在前n-1个正整数里面找，得到递归式dp\[n][k] = dp\[n-1][k]+dp\[n-1][k-1]+dp\[n-1][k-2]+…+dp\[n-1][k+1-n+1]+dp\[n-1][k-n+1]，再继续约简得到dp\[n][k+1] = dp\[n][k]+dp\[n-1][k+1]-dp\[n-1][k+1-n]

  > 需要注意数组越界和数字大小问题

  ```java
  class Solution {
      public int kInversePairs(int n, int k) {
          int mod = 1000000007;
          if (k > n * (n - 1) / 2 || k < 0) {
              return 0;
          }
          if (k == 0 || k == n * (n - 1) / 2) {
              return 1;   
          }
          long[][] dp = new long[n + 1][k + 1];
          dp[2][0] = 1;
          dp[2][1] = 1;
          for (int i = 3; i <= n; i++) {
              dp[i][0] = 1;
              for (int j = 1; j <= Math.min(k, i * (i - 1) / 2); j++) {
                  dp[i][j] = dp[i][j - 1] + dp[i - 1][j];
                  if (j >= i) {
                      dp[i][j] -= dp[i - 1][j - i];
                  }
                  dp[i][j] = (dp[i][j] + mod) % mod;
              }
          }
          return (int)dp[n][k];
      }
  }
  ```

### 477. Total Hamming Distance(Medium)

- 题意：给定数组，求出数组所有数字对的海明距离之和

- 解法：总共32位，分别对每一位求出海明距离，每次判断当前数字某位是否为1，再给结果加上前面的1的数目或者0的数目

  ```java
  class Solution {
      public int totalHammingDistance(int[] nums) {
          int res = 0;
          int temp = 1;
          for(int j = 0; j < 32; j++) {
              int count = 0;
              for(int i = 0; i < nums.length; i++) {
                  if((temp & nums[i]) > 0) {
                      res += i - count;
                      count++;
                  } else {
                      res += count;
                  }
              }
              temp <<= 1;
          }
          return res;
      }
  }
  ```

### 542. 01 Matrix(Medium)

- 题意：给定01矩阵，对每个位置求出该元素到0的最小距离

- 解法1：使用bfs

  ```java
  public class Solution {
      public int[][] updateMatrix(int[][] matrix) {
          int m = matrix.length;
          int n = matrix[0].length;
          
          Queue<int[]> queue = new LinkedList<>();
          for (int i = 0; i < m; i++) {
              for (int j = 0; j < n; j++) {
                  if (matrix[i][j] == 0) {
                      queue.offer(new int[] {i, j});
                  }
                  else {
                      matrix[i][j] = Integer.MAX_VALUE;
                  }
              }
          }
          
          int[][] dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
          
          while (!queue.isEmpty()) {
              int[] cell = queue.poll();
              for (int[] d : dirs) {
                  int r = cell[0] + d[0];
                  int c = cell[1] + d[1];
                  if (r < 0 || r >= m || c < 0 || c >= n || 
                      matrix[r][c] <= matrix[cell[0]][cell[1]] + 1) continue;
                  queue.add(new int[] {r, c});
                  matrix[r][c] = matrix[cell[0]][cell[1]] + 1;
              }
          }
          
          return matrix;
      }
  }
  ```

- 解法2：动态规划走两遍，一遍从左上角走起，一遍从右下角走起

  ```java
  public int[][] updateMatrix(int[][] matrix) {
      if (matrix.length == 0 || matrix[0].length == 0) {
          return matrix;
      }
      int[][] dis = new int[matrix.length][matrix[0].length];
      int range = matrix.length * matrix[0].length;
      
      for (int i = 0; i < matrix.length; i++) {
          for (int j = 0; j < matrix[0].length; j++) {
              if (matrix[i][j] == 0) {
                  dis[i][j] = 0;
              } else {
                  int upCell = (i > 0) ? dis[i - 1][j] : range;
                  int leftCell = (j > 0) ? dis[i][j - 1] : range;
                  dis[i][j] = Math.min(upCell, leftCell) + 1;
              }
          }
      }
      
      for (int i = matrix.length - 1; i >= 0; i--) {
          for (int j = matrix[0].length - 1; j >= 0; j--) {
              if (matrix[i][j] == 0) {
                  dis[i][j] = 0;
              } else {
                  int downCell = (i < matrix.length - 1) ? dis[i + 1][j] : range;
                  int rightCell = (j < matrix[0].length - 1) ? dis[i][j + 1] : range;
                  dis[i][j] = Math.min(Math.min(downCell, rightCell) + 1, dis[i][j]);
              }
          }
      }
      
      return dis;
  }
  ```

### 316. Remove Duplicate Letters(Hard)

- 题意：给定字符串，求出字符串中由所有不同字母组成的最小字典序子序列

- 解法1：贪心法，在保证后面字符串包括其他所有独特字符的前提下，每次找到当前最小字符，使用递归求解

  ```java
  public String removeDuplicateLetters(String s) {
          int[] cnt = new int[26];
          int pos = 0; // the position for the smallest s[i]
          for (int i = 0; i < s.length(); i++) cnt[s.charAt(i) - 'a']++;
          for (int i = 0; i < s.length(); i++) {
              if (s.charAt(i) < s.charAt(pos)) pos = i;
              if (--cnt[s.charAt(i) - 'a'] == 0) break;
          }
          return s.length() == 0 ? "" : s.charAt(pos) + removeDuplicateLetters(s.substring(pos + 1).replaceAll("" + s.charAt(pos), ""));
      }
  ```

- 解法2：观察问题，发现最后求出的字符串一定符合原来的顺序，只是位置可能不是相邻的，联想到栈的顺序性，所以可以使用栈来保存，如果能保证后面序列中还有当前栈顶元素才允许弹栈，所以也要先记录元素个数

  ```java
      public String removeDuplicateLetters(String s) {
          Stack<Character> stack = new Stack<>();
          int[] count = new int[26];
          char[] arr = s.toCharArray();
          for(char c : arr) {
              count[c-'a']++;
          }
          boolean[] visited = new boolean[26];
          for(char c : arr) {
              count[c-'a']--;
              if(visited[c-'a']) {
                  continue;
              }
              while(!stack.isEmpty() && stack.peek() > c && count[stack.peek()-'a'] > 0) {
                  visited[stack.peek()-'a'] = false;
                  stack.pop();
              }
              stack.push(c);
              visited[c-'a'] = true;
          }
          StringBuilder sb = new StringBuilder();
          for(char c : stack) {
              sb.append(c);
          }
          return sb.toString();
      }
  ```

### 598. Range Addition II(Easy)

- 题意：给定m，n表示全为0的m*n矩阵，给定很多个数对[i，j]使得矩阵坐标在数对左上角的元素+1，求出最后矩阵最大元素对应的数字个数有多少

- 解法：因为每次左上角都会+1，所以只要求出最小的有效数对围成的范围即可

  ```java
  class Solution {
      public int maxCount(int m, int n, int[][] ops) {
          int min_x = m;
          int min_y = n;
          for(int i = 0; i < ops.length; i++) {
              if(ops[i][0] > 0 && ops[i][1] > 0) {
                  min_x = Math.min(min_x, ops[i][0]);
                  min_y = Math.min(min_y, ops[i][1]);
              }
          }
          return min_x * min_y;
      }
  }
  ```

### 717. 1-bit and 2-bit Characters(Easy)

- 题意：给定01编码数组作为字符串，数组一定以0为结尾，每个字符可以由1个元素0或者两个字符10、11组成，判断字符串最后一个元素是否只由1个元素组成

- 解法：倒着看，找出最后一个0前有多少个1，如果有奇数个1说明最后一定是10，如果是偶数个则说明最后是110

  ```java
  class Solution {
      public boolean isOneBitCharacter(int[] bits) {
          for(int i = bits.length - 2; i >= 0; i--) {
              if(bits[i] == 0) {
                  return (bits.length - 2 - i) % 2 == 0;
              }
          }
          return (bits.length - 1) % 2 == 0;
      }
  }
  ```

### 219. Contains Duplicate II(Easy)

- 题意：给定数组nums和k，判断nums中是否有两个相等的元素其下标差不超过k
- 解法1：使用map记住元素上一次下标
- 解法2：使用一个大小为k的set

### 720. Longest Word in Dictionary(Easy)

- 题意：求出字符串数组中最长的可以由字符一个一个拼接起来的字符串，拼接过程中的字符串也都必须在数组中

- 解法：先进行排序，然后将有效字符串存起来

  ```java
  class Solution {
      public String longestWord(String[] words) {
          Arrays.sort(words);
          HashSet<String> set = new HashSet();
          String res = "";
          for(String word: words) {
              if(word.length() == 1 || set.contains(word.substring(0, word.length() - 1))) {
                  set.add(word);
                  if(res.length() < word.length()) {
                      res = word;
                  }
              }
          }
          return res;
      }
  }
  ```

### 795. Number of Subarrays with Bounded Maximum(Medium)

- 题意：求出最大值在给定界限内的子数组的数目

- 解法：分为三种情况，当前值在界限内，当前值小于界限，当前值大于界限，由于子数组的连续性，所以可以使用滑动窗口求解，求出加入当前值的所有有效子数组数目可以通过窗口大小来求出

  ```java
  class Solution {
      public int numSubarrayBoundedMax(int[] A, int L, int R) {
          int start = 0;
          int count = 0;
          int res = 0;
          for(int i = 0; i < A.length; i++) {
              if(A[i] >= L && A[i] <= R) {
                  res += i - start + 1;
                  count = i - start + 1;
              }
              else if(A[i] < L) {
                  res += count;
              }
              else {
                  start = i + 1;
                  count = 0;
              }
          }
          return res;
      }
  }
  ```

### 876. Middle of the Linked List(Easy)

- 题意：求出链表的中间节点(偶数个则输出后一个)

- 解法：使用快慢指针

  ```java
  class Solution {
      public ListNode middleNode(ListNode head) {
          ListNode fast = head;
          ListNode slow = head;
          while(fast != null && fast.next != null) {
              fast = fast.next.next;
              slow = slow.next;
          }
          return slow;
      }
  }
  ```

### 767. Reorganize String(Medium)

- 题意：给定字符串，将其重排列为所有相邻字符不同的字符串，如果不存在则返回空串

- 解法1：对字符串字符进行计数，并将对应字符编码存到计数数组中，然后排序，最后构造字符串

  ```java
  class Solution {
      public String reorganizeString(String S) {
          int[] letters = new int[26];
          for(char s: S.toCharArray()) {
              letters[s-'a'] += 100;
          }
          for(int i = 0; i < letters.length; i++) {
              letters[i] += i;
          }
          
          Arrays.sort(letters);
          char[] res = new char[S.length()];
          int index = 1;
          for(int code: letters) {
              int num = code / 100;
              char letter = (char)(code % 100 + 'a');
              if(num > (S.length() + 1) / 2) {
                  return "";
              }
              for(int i = 0; i < num; i++) {
                  if(index >= S.length()) {
                      index = 0;
                  }
                  res[index] = letter;
                  index += 2;
              }
          }
          return String.valueOf(res);
      }
  }
  ```

- 解法2：贪心法，每次都将当前字符数最多的两个字符放入结果中，这样可以保证符合字符交替的规则，可以使用堆来实现

  ```java
  class Solution {
      public String reorganizeString(String S) {
          int N = S.length();
          int[] count = new int[26];
          for (char c: S.toCharArray()) count[c-'a']++;
          PriorityQueue<MultiChar> pq = new PriorityQueue<MultiChar>((a, b) ->
              a.count == b.count ? a.letter - b.letter : b.count - a.count);
  
          for (int i = 0; i < 26; ++i) if (count[i] > 0) {
              if (count[i] > (N + 1) / 2) return "";
              pq.add(new MultiChar(count[i], (char) ('a' + i)));
          }
  
          StringBuilder ans = new StringBuilder();
          while (pq.size() >= 2) {
              MultiChar mc1 = pq.poll();
              MultiChar mc2 = pq.poll();
              /*This code turns out to be superfluous, but explains what is happening
              if (ans.length() == 0 || mc1.letter != ans.charAt(ans.length() - 1)) {
                  ans.append(mc1.letter);
                  ans.append(mc2.letter);
              } else {
                  ans.append(mc2.letter);
                  ans.append(mc1.letter);
              }*/
              ans.append(mc1.letter);
              ans.append(mc2.letter);
              if (--mc1.count > 0) pq.add(mc1);
              if (--mc2.count > 0) pq.add(mc2);
              }
          }
  
          if (pq.size() > 0) ans.append(pq.poll().letter);
          return ans.toString();
      }
  }
  class MultiChar {
      int count;
      char letter;
      MultiChar(int ct, char ch) {
          count = ct;
          letter = ch;
      }
  }
  ```

### 214. Shortest Palindrome(Hard)

- 题意：给定字符串s，可以在s前面加字符，求这样形成的最短回文串

- 解法1：使用指针法，分奇偶讨论

  ```java
  class Solution {
      public String shortestPalindrome(String s) {
          int N = s.length();
          String res_1 = "";
          for(int i = (N - 1) / 2; i >= 0; i--) {
              int l = i - 1;
              int h = i + 1;
              while(l >= 0) {
                  if(s.charAt(l) == s.charAt(h)) {
                      l--;
                      h++;
                  } else {
                      break;
                  }
              }
              if(l < 0) {
                  StringBuilder sb = new StringBuilder();
                  for(int j = N - 1; j >= h; j--) {
                      sb.append(s.charAt(j));
                  }
                  sb.append(s);
                  res_1 = sb.toString();
                  break;
              }
          }
          String res_2 = "";
          for(int i = N / 2; i >= 0; i--) {
              int l = i - 1;
              int h = i;
              while(l >= 0) {
                  if(s.charAt(l) == s.charAt(h)) {
                      l--;
                      h++;
                  } else {
                      break;
                  }
              }
              if(l < 0) {
                  StringBuilder sb = new StringBuilder();
                  for(int j = N - 1; j >= h; j--) {
                      sb.append(s.charAt(j));
                  }
                  sb.append(s);
                  res_2 = sb.toString();
                  break;
              }
          }
          return res_1.length() > res_2.length() ? res_2 : res_1;
      }
  }
  ```

- 解法2：关键是要求出从第一个字符开始的最长回文子串，将字符串翻转为rs，即求一个从s第一个字符开始的子串与到rs最后一个字符的子串的最长串，将s和rs拼接，使用KMP算法的索引数组，就变成了求最后一个字符的索引位置，注意要保证索引位置在字符串的前一半位置

  >c a  t a c b # b c a  t a c
  >
  >0 0 0 0 1 0 0 0 1 2 3 4 5

  ```java
  class Solution {
      public String shortestPalindrome(String s) {
          String temp = s + "#" + new StringBuilder(s).reverse().toString();
          int[] table = getTable(temp);
          return new StringBuilder(s.substring(table[table.length - 1] + 1)).reverse().toString() + s;
      }
  
      public int[] getTable(String s){
          int[] next = new int[s.length()];
          next[0] = -1;
          int j = 0;
          int k = -1;
          while(j < s.length() - 1) {
            if(k == -1 || s.charAt(j) == s.charAt(k)) {
              //说明j之前与k之前的所有字符都一样
              next[++j] = ++k;
            } else {
              //否则k返回上个位置
              k = next[k];
            }
          }
          return next;
      }
  }
  ```

### 1005. Maximize Sum Of Array After K Negations(Easy)

- 题意：给定数组A，将数组中某一元素取相反数并进行K次，求数组和的最大值

- 解法1：暴力解决，先排序，然后将负数取反，如果还剩下奇数次则再排序将最小的元素取反

- 解法2：鉴于每次都是对最小的元素进行处理，可以使用优先队列来维护，每次都将队列首元素取反再放回队列中重复K次

  ```java
  public int largestSumAfterKNegations(int[] A, int K) {
    PriorityQueue<Integer> pq = new PriorityQueue<Integer>();
  
    for(int x: A) pq.add(x);
    while( K-- > 0) pq.add(-pq.poll());
  
    int sum = 0;
    for(int i = 0; i < A.length; i++){
      sum += pq.poll();
    }
    return sum;
  }
  ```

### 835. Image Overlap(Medium)

- 题意：给定两个二维01数组，可以对数组进行平移操作，平移后两个数组必须完全一样，求最后数组中最多有多少个1

- 解法：由于数组A每个位置的元素都可能映射到数组B的任意位置上去，因此需要按照位置来判断是否重叠，先用两个list存所有1的位置，然后计算出位置之间的距离即平移操作的距离，这样就计算出了所有平移操作得到的1的个数

  ```java
  class Solution {
      public int largestOverlap(int[][] A, int[][] B) {
          int N = A.length;
          List<Integer> LA = new ArrayList<>();
          List<Integer> LB = new ArrayList<>();
          HashMap<Integer, Integer> count = new HashMap<>();
          for (int i = 0; i < N * N; ++i) 
              if (A[i / N][i % N] == 1) 
                  LA.add(i / N * 100 + i % N);
          for (int i = 0; i < N * N; ++i) 
              if (B[i / N][i % N] == 1) 
                  LB.add(i / N * 100 + i % N);
        
          for (int i : LA)
              for (int j : LB)
                  count.put(i - j, count.getOrDefault(i - j, 0) + 1);
          int res = 0;
          for (int i : count.values()) 
              res = Math.max(res, i);
          return res;
      }
  }
  ```

### 453. Minimum Moves to Equal Array Elements(Easy)

- 题意：给定长度为n的数组，将其中n-1个元素都+1视为一个操作，求最少经过多少次操作可以让数组元素都相等
- 解法：每次n-1个元素都+1其实就等价于有1个元素-1了，所以只要求所有元素到数组最小值的距离即可

### 462. Minimum Moves to Equal Array Elements II(Medium)

- 题意：给定数组，每次可以给一个元素+1或者-1，求最少经过多少次操作可以让数组元素都相等

- 解法：考虑最后都变成mid，那么就是mid - nums[0] + nums[n-1] - mid，这样就不需要算出mid了，直接nums[n-1]-nums[0]，因此转化为了两点相遇的问题，这个mid实际就是中位数了

  ```java
  class Solution {
      public int minMoves2(int[] nums) {
          Arrays.sort(nums);
          int i = 0;
          int j = nums.length - 1;
          int res = 0;
          while(i < j) {
              res += nums[j] - nums[i];
              i++;
              j--;
          }
          return res;
      }
  }
  ```

### 850. Rectangle Area II(Hard)

- 题意：给定矩形数组，包括矩形的左下坐标和右上坐标，求矩形数组的覆盖面积

- 解法1：使用容斥原理，超时

- 解法2：将横纵坐标***压缩***，排序后使用bitmap来解决，计算面积时解压

  ```java
  class Solution {
      public int rectangleArea(int[][] rectangles) {
          int N = rectangles.length;
          Set<Integer> Xvals = new HashSet();
          Set<Integer> Yvals = new HashSet();
  
          for (int[] rec: rectangles) {
              Xvals.add(rec[0]);
              Xvals.add(rec[2]);
              Yvals.add(rec[1]);
              Yvals.add(rec[3]);
          }
  
          Integer[] imapx = Xvals.toArray(new Integer[0]);
          Arrays.sort(imapx);
          Integer[] imapy = Yvals.toArray(new Integer[0]);
          Arrays.sort(imapy);
  
          Map<Integer, Integer> mapx = new HashMap();
          Map<Integer, Integer> mapy = new HashMap();
          for (int i = 0; i < imapx.length; ++i)
              mapx.put(imapx[i], i);
          for (int i = 0; i < imapy.length; ++i)
              mapy.put(imapy[i], i);
  
          boolean[][] grid = new boolean[imapx.length][imapy.length];
          for (int[] rec: rectangles)
              for (int x = mapx.get(rec[0]); x < mapx.get(rec[2]); ++x)
                  for (int y = mapy.get(rec[1]); y < mapy.get(rec[3]); ++y)
                      grid[x][y] = true;
  
          long ans = 0;
          for (int x = 0; x < grid.length; ++x)
              for (int y = 0; y < grid[0].length; ++y)
                  if (grid[x][y])
                      ans += (long) (imapx[x+1] - imapx[x]) * (imapy[y+1] - imapy[y]);
  
          ans %= 1_000_000_007;
          return (int) ans;
      }
  }
  ```

- 解法3：直线扫描，按照纵坐标从小到大进行扫描，每个矩形都有两个操作(下方加入和上方移除)，将这两个操作记录，进行排序，然后按照顺序进行操作，将加入操作对应的横坐标区间放到active数组记录，然后就是合并区间数组求面积了

  ```java
  class Solution {
      public int rectangleArea(int[][] rectangles) {
          int OPEN = 0, CLOSE = 1;
          int[][] events = new int[rectangles.length * 2][];
          int t = 0;
          for (int[] rec: rectangles) {
              events[t++] = new int[]{rec[1], OPEN, rec[0], rec[2]};
              events[t++] = new int[]{rec[3], CLOSE, rec[0], rec[2]};
          }
  
          Arrays.sort(events, (a, b) -> Integer.compare(a[0], b[0]));
  
          List<int[]> active = new ArrayList();
          int cur_y = events[0][0];
          long ans = 0;
          for (int[] event: events) {
              int y = event[0], typ = event[1], x1 = event[2], x2 = event[3];
  
              // Calculate query
              long query = 0;
              int cur = -1;
              for (int[] xs: active) {
                  cur = Math.max(cur, xs[0]);
                  query += Math.max(xs[1] - cur, 0);
                  cur = Math.max(cur, xs[1]);
              }
  
              ans += query * (y - cur_y);
  
              if (typ == OPEN) {
                  active.add(new int[]{x1, x2});
                  Collections.sort(active, (a, b) -> Integer.compare(a[0], b[0]));
              } else {
                  for (int i = 0; i < active.size(); ++i)
                      if (active.get(i)[0] == x1 && active.get(i)[1] == x2) {
                          active.remove(i);
                          break;
                      }
              }
  
              cur_y = y;
          }
  
          ans %= 1_000_000_007;
          return (int) ans;
      }
  }
  ```

- 解法4：类似解法3的思想，将加入移除和合并区间的操作用[线段树](https://www.cnblogs.com/xiaoyao24256/p/6590885.html)来解决

  ```java
  class Solution {
      public int rectangleArea(int[][] rectangles) {
          int OPEN = 1, CLOSE = -1;
          int[][] events = new int[rectangles.length * 2][];
          Set<Integer> Xvals = new HashSet();
          int t = 0;
          for (int[] rec: rectangles) {
              events[t++] = new int[]{rec[1], OPEN, rec[0], rec[2]};
              events[t++] = new int[]{rec[3], CLOSE, rec[0], rec[2]};
              Xvals.add(rec[0]);
              Xvals.add(rec[2]);
          }
  
          Arrays.sort(events, (a, b) -> Integer.compare(a[0], b[0]));
  
          Integer[] X = Xvals.toArray(new Integer[0]);
          Arrays.sort(X);
          Map<Integer, Integer> Xi = new HashMap();
          for (int i = 0; i < X.length; ++i)
              Xi.put(X[i], i);
  
          Node active = new Node(0, X.length - 1, X);
          long ans = 0;
          long cur_x_sum = 0;
          int cur_y = events[0][0];
  
          for (int[] event: events) {
              int y = event[0], typ = event[1], x1 = event[2], x2 = event[3];
              ans += cur_x_sum * (y - cur_y);
              cur_x_sum = active.update(Xi.get(x1), Xi.get(x2), typ);
              cur_y = y;
  
          }
  
          ans %= 1_000_000_007;
          return (int) ans;
      }
  }
  
  class Node {
      int start, end;
      Integer[] X;
      Node left, right;
      int count;
      long total;
  
      public Node(int start, int end, Integer[] X) {
          this.start = start;
          this.end = end;
          this.X = X;
          left = null;
          right = null;
          count = 0;
          total = 0;
      }
  
      public int getRangeMid() {
          return start + (end - start) / 2;
      }
  
      public Node getLeft() {
          if (left == null) left = new Node(start, getRangeMid(), X);
          return left;
      }
  
      public Node getRight() {
          if (right == null) right = new Node(getRangeMid(), end, X);
          return right;
      }
  
      public long update(int i, int j, int val) {
          if (i >= j) return 0;
          if (start == i && end == j) {
              count += val;
          } else {
              getLeft().update(i, Math.min(getRangeMid(), j), val);
              getRight().update(Math.max(getRangeMid(), i), j, val);
          }
  
          if (count > 0) total = X[end] - X[start];
          else total = getLeft().total + getRight().total;
  
          return total;
      }
  }
  ```

### 792. Number of Matching Subsequences(Medium)

- 题意：给定字符串S和字符串数组words，求出words中有多少个是S的子序列
- 解法1：暴力一个一个判断
- 解法2：由于题中字符只包含小写字母，所以可以同时判断words里面所有的词是否符合规则，按照当前需要的字符使用数组分别保存，最后不需要字符的字符串数就是[结果](https://leetcode.com/problems/number-of-matching-subsequences/discuss/117634/Efficient-and-simple-go-through-words-in-parallel-with-explanation)

### 1002. Find Common Characters(Easy)

- 题意：给定字符串数组，求出所有公共字符，可以重复
- 解法：由于字符只由小写字母组成，因此可以使用二维数组来计数，然后求出每个字符的最小计数值，再按照该值加入结果即可

### 350. Intersection of Two Arrays II(Easy)

- 题意：给定两个数组，求出所有公共元素，可重复
- 解法：使用hashmap记住第一个数组中每个元素值的个数，然后遍历第二个数组时要保证元素值个数不能大于第一个数组中map记住的值

### 988. Smallest String Starting From Leaf(Medium)

- 题意：给定二叉树，结点表示字符，求出从叶子节点开始到根结束的最小字典序字符串

- 解法：使用深度遍历求出每一个字符串，找到最小的

  > 该题不能使用分治法，因为是从叶子节点开始的，所以兄弟节点间所处的字符串位置可能不一样
  >
  > 深度遍历时需要保证结果是从叶子节点开始的

  ```java
  class Solution {
      public String smallestFromLeaf(TreeNode root) {
          dfs(root, "");
          return res;
      }
      
      String res = "";
      private void dfs(TreeNode root, String temp) {
          if(root == null) {
              return;
          }
          if(root.left == null && root.right == null) {
              temp = (char)(root.val + 'a') + temp;
              if(res.equals("")) {
                  res = temp;
              } else if(!temp.equals("") && temp.compareTo(res) < 0) {
                  res = temp;
              }
              return;
          }
          dfs(root.left, (char)(root.val + 'a') + temp);
          dfs(root.right, (char)(root.val + 'a') + temp);
      }
  }
  ```

### 99. Recover Binary Search Tree(Hard)

- 题意：给定二叉搜索树，里面有两个结点交换了，将该树进行修复

- 解法：使用中序遍历，这样就相当于修复一个有序数组中的逆序对了，只有两种情况，即逆序对相邻与否

  > 使用深度搜索，有好多种情况，如两个结点是祖先和子孙关系、兄弟关系、没有关系只有公共祖先，讨论不过来

  ```java
  class Solution {
      public void recoverTree(TreeNode root) {
          inorder(root);
          if(three == null) {
              swap(one, two);
          } else {
              swap(one, four);
          }
      }
      
      TreeNode one = null;
      TreeNode two = null;
      TreeNode three = null;
      TreeNode four = null;
      TreeNode pre = new TreeNode(Integer.MIN_VALUE);
      private void inorder(TreeNode root) {
          if(root == null) {
              return;
          }
          inorder(root.left);
          if(pre.val > root.val) {
              if(one == null) {
                  one = pre;
                  two = root;
              } else {
                  three = pre;
                  four = root;
              }
          }
          pre = root;
          inorder(root.right);
      }
      
      private void swap(TreeNode a, TreeNode b) {
          int val = a.val;
          a.val = b.val;
          b.val = val;
      }
  }
  ```

### 221. Maximal Square(Medium)

- 题意：给定01二维数组，求出数组中全部由1组成的最大正方形面积

- 解法1：求出最大的长方形就求出了最大的正方形，所以可以参考第85题最大长方形的解法

- 解法2：动态规划，dp[i, j]表示以i，j点为右下角的最大正方形边长

  >dp\[i][j] = min(dp\[i][j - 1], dp\[i - 1][j], dp\[i - 1][j - 1]) + 1
  >
  >由于实际上dp数组只由上一行的元素和上一个元素来决定，所以可以只存储上一行元素数组，减少空间复杂度

  ```java
  public class Solution {
      public int maximalSquare(char[][] matrix) {
          int rows = matrix.length, cols = rows > 0 ? matrix[0].length : 0;
          int[][] dp = new int[rows + 1][cols + 1];
          int maxsqlen = 0;
          for (int i = 1; i <= rows; i++) {
              for (int j = 1; j <= cols; j++) {
                  if (matrix[i-1][j-1] == '1'){
                      dp[i][j] = Math.min(Math.min(dp[i][j - 1], dp[i - 1][j]), dp[i - 1][j - 1]) + 1;
                      maxsqlen = Math.max(maxsqlen, dp[i][j]);
                  }
              }
          }
          return maxsqlen * maxsqlen;
      }
  }
  ```

### 730. Count Different Palindromic Subsequences(Hard)

- 题意：求出字符串中不同非空回文子序列的数目

- 解法：使用动态规划

  >s.charAt(i) != s.charAt(j):  dp\[i][j] = dp\[i][j - 1] + dp\[i + 1][j] - dp\[i + 1][j - 1]
  >
  >s.charAt(i) != s.charAt(j):  很多种情况，需要保证去重复

  ```java
  class Solution {
      public int countPalindromicSubsequences(String s) {
          int len = s.length();
          int[][] dp = new int[len][len];
  
          char[] chs = s.toCharArray();
          for(int i = 0; i < len; i++){
              dp[i][i] = 1;   // Consider the test case "a", "b" "c"...
          }
  
          for(int distance = 1; distance < len; distance++){
              for(int i = 0; i < len - distance; i++){
                  int j = i + distance;
                  if(chs[i] == chs[j]){
                      int low = i + 1;
                      int high = j - 1;
  
                /* Variable low and high here are used to get rid of the duplicate*/
  
                      while(low <= high && chs[low] != chs[j]){
                          low++;
                      }
                      while(low <= high && chs[high] != chs[j]){
                          high--;
                      }
                      if(low > high){
                          // consider the string from i to j is "a...a" "a...a"... where there is no character 'a' inside the leftmost and rightmost 'a'
                         /* eg:  "aba" while i = 0 and j = 2:  dp[1][1] = 1 records the palindrome{"b"}, 
                           the reason why dp[i + 1][j  - 1] * 2 counted is that we count dp[i + 1][j - 1] one time as {"b"}, 
                           and additional time as {"aba"}. The reason why 2 counted is that we also count {"a", "aa"}. 
                           So totally dp[i][j] record the palindrome: {"a", "b", "aa", "aba"}. 
                           */ 
  
                          dp[i][j] = dp[i + 1][j - 1] * 2 + 2;  
                      } 
                      else if(low == high){
                          // consider the string from i to j is "a...a...a" where there is only one character 'a' inside the leftmost and rightmost 'a'
                         /* eg:  "aaa" while i = 0 and j = 2: the dp[i + 1][j - 1] records the palindrome {"a"}.  
                           the reason why dp[i + 1][j  - 1] * 2 counted is that we count dp[i + 1][j - 1] one time as {"a"}, 
                           and additional time as {"aaa"}. the reason why 1 counted is that 
                           we also count {"aa"} that the first 'a' come from index i and the second come from index j. So totally dp[i][j] records {"a", "aa", "aaa"}
                          */
                          dp[i][j] = dp[i + 1][j - 1] * 2 + 1;  
                      }
                      else{
                          // consider the string from i to j is "a...a...a... a" where there are at least two character 'a' close to leftmost and rightmost 'a'
                         /* eg: "aacaa" while i = 0 and j = 4: the dp[i + 1][j - 1] records the palindrome {"a",  "c", "aa", "aca"}. 
                            the reason why dp[i + 1][j  - 1] * 2 counted is that we count dp[i + 1][j - 1] one time as {"a",  "c", "aa", "aca"}, 
                            and additional time as {"aaa",  "aca", "aaaa", "aacaa"}.  Now there is duplicate :  {"aca"}, 
                            which is removed by deduce dp[low + 1][high - 1]. So totally dp[i][j] record {"a",  "c", "aa", "aca", "aaa", "aaaa", "aacaa"}
                            */
                          dp[i][j] = dp[i + 1][j - 1] * 2 - dp[low + 1][high - 1]; 
                      }
                  }
                  else{
                      dp[i][j] = dp[i][j - 1] + dp[i + 1][j] - dp[i + 1][j - 1];  //s.charAt(i) != s.charAt(j)
                  }
                  dp[i][j] = dp[i][j] < 0 ? dp[i][j] + 1000000007 : dp[i][j] % 1000000007;
              }
          }
  
          return dp[0][len - 1];
      }
  }
  ```

### 560. Subarray Sum Equals K(Medium)

- 题意：给定数组nums，求出其子数组和为k的数目

- 解法1：求出所有可能的和

- 解法2：sum[i]表示从0~i子数组的和，若sum[j] - sum[i] == k，则说明子数组i~j符合条件，先求出所有sum[i]，然后就转化为了数组两数之差的问题，可以使用hashmap来解决，需要先把0放入map中保证sum-0的情况

  ```java
  public class Solution {
      public int subarraySum(int[] nums, int k) {
          int count = 0, sum = 0;
          HashMap<Integer, Integer> map = new HashMap<>();
          map.put(0, 1);
          for (int i = 0; i < nums.length; i++) {
              sum += nums[i];
              if (map.containsKey(sum - k))
                  count += map.get(sum - k);
              map.put(sum, map.getOrDefault(sum, 0) + 1);
          }
          return count;
      }
  }
  ```

### 763. Partition Labels(Medium)

- 题意：给定字符串，将其划分为子串，两个子串间没有相同的字符，求划分最多子串的划分方法

- 解法1：求出每种字符的起始和终止位置，然后区间合并

- 解法2：求出每种字符的终止位置，然后再遍历一遍求出区间

  ```java
  class Solution {
      public List<Integer> partitionLabels(String S) {
          int[] last = new int[26];
          for (int i = 0; i < S.length(); ++i)
              last[S.charAt(i) - 'a'] = i;
          
          int j = 0, anchor = 0;
          List<Integer> ans = new ArrayList();
          for (int i = 0; i < S.length(); ++i) {
              j = Math.max(j, last[S.charAt(i) - 'a']);
              if (i == j) {
                  ans.add(i - anchor + 1);
                  anchor = i + 1;
              }
          }
          return ans;
      }
  }
  ```

### 496. Next Greater Element I(Easy)

- 题意：给定不重复数组，求出数组一个子集每个元素在原数组中下一个比它大的元素

- 解法：求出每个元素下一个更大的元素即可，可以使用栈保存前面没有求出来的元素，这是一个递减子序列

  > 如[9,8,7,3,2,1,6]，先把[9,8,7,3,2,1]入栈，遇到6再把[1,2,3]出栈

  ```java
  class Solution {
      public int[] nextGreaterElement(int[] findNums, int[] nums) {
          Map<Integer, Integer> map = new HashMap<>(); // map from x to next greater element of x
          Stack<Integer> stack = new Stack<>();
          for (int num : nums) {
              while (!stack.isEmpty() && stack.peek() < num)
                  map.put(stack.pop(), num);
              stack.push(num);
          }   
          for (int i = 0; i < findNums.length; i++)
              findNums[i] = map.getOrDefault(findNums[i], -1);
          return findNums;
      }
  }
  ```

### 556. Next Greater Element III(Medium)

- 题意：给定32位正数n，求出由n中数字组成的比n大的最小数字，如果没有返回-1

- 解法：先求出n的从个位开始的逆序列，逆序列到第len-i位，然后求出逆序列中刚好比第len-i+1位的数更大的数，将其和len-i位交换，然后逆序列倒序即

  > 可以方便地将数字n转化为char数组

  ```java
  public class Solution {
      public int nextGreaterElement(int n) {
          char[] number = (n + "").toCharArray();
          
          int i, j;
          // I) Start from the right most digit and 
          // find the first digit that is
          // smaller than the digit next to it.
          for (i = number.length-1; i > 0; i--)
              if (number[i-1] < number[i])
                 break;
  
          // If no such digit is found, its the edge case 1.
          if (i == 0)
              return -1;
              
           // II) Find the smallest digit on right side of (i-1)'th 
           // digit that is greater than number[i-1]
          int x = number[i-1], smallest = i;
          for (j = i+1; j < number.length; j++)
              if (number[j] > x && number[j] <= number[smallest])
                  smallest = j;
              else
                  break;
          
          // III) Swap the above found smallest digit with 
          // number[i-1]
          swap(number, i-1, smallest);
          
          // IV) Sort the digits after (i-1) in ascending order
          int start = i;
          int end = number.length - 1;
          while(start < end) {
              swap(number, start++, end--);
          }
          
          long val = Long.parseLong(new String(number));
          return (val <= Integer.MAX_VALUE) ? (int) val : -1;
      }
      
      private void swap(char[] number, int a, int b) {
          char temp = number[a];
          number[a] = number[b];
          number[b] = temp;
      }
  }
  ```

### 814. Binary Tree Pruning(Medium)

- 题意：给定01二叉树，将所有只包含0的子树移除
- 解法：从子树到根，所以使用后序遍历

### 405. Convert a Number to Hexadecimal(Easy)

- 题意：给定32位整数，将其转化为16进制输出

- 解法：使用移位运算

  ```java
  public class Solution {
      char[] map = {'0','1','2','3','4','5','6','7','8','9','a','b','c','d','e','f'};
      public String toHex(int num) {
          if(num == 0) return "0";
          String result = "";
          while(num != 0){
              result = map[(num & 15)] + result; 
              num = (num >>> 4);
          }
          return result;
      }
  }
  ```

### 136. Single Number(Easy)

- 题意：给定数组，所有数字都出现两次，只有一个数字只出现一次，找到这个数
- 解法：使用异或特性求解

### 137. Single Number II(Medium)

- 题意：给定数组，所有数字都出现三次，只有一个数字只出现一次，找到这个数

- 解法：这种问题的解法使用有限状态自动机，这里总共有三种状态(出现1次、2次、3次)来记录当前的状态

  > State    temp    next
  >
  >   0 0          1         0 1
  >
  >   0 1	      1         1 0
  >
  >   1 0          1         0 0
  >
  > 遇到temp=0则状态不变，由此推出求next的等式

  ```java
  class Solution {
      public int singleNumber(int[] nums) {
          int a = 0;
          int b = 0;
          for(int num: nums) {
              int ta = a;
              a = (~a&b&num) + (a&~num);
              b = (~ta&~b&num) + (b&~num);
          }
          return b;
      }
  }
  ```

### 260. Single Number III(Medium)

- 题意：给定数组，数组中每个数字都出现两次，只有两个不同的数字只出现一次，求出这两个数字

- 解法：将整个数组分为两组，分别异或求出数字

  > 假设这两个数字为a，b
  >
  > 首先求出整个数组的异或即a ^ b
  >
  > 然后可以得到a和b之间第i位的bit不同，构造出n = 2^i
  >
  > 通过判断当前数字num&n是否为0来进行分组

  ```java
  class Solution {
      public int[] singleNumber(int[] nums) {
          int a = 0;
          for(int num: nums) {
              a ^= num;
          }
          int n = 1;
          while(a % 2 == 0) {
              a >>= 1;
              n <<= 1;
          }
          int x = 0;
          int y = 0;
          for(int num: nums) {
              if((n & num) == 0) {
                  x ^= num;
              } else {
                  y ^= num;
              }
          }
          return new int[]{x, y};
      }
  }
  ```

### 79. Word Search(Medium)

- 题意：给定二维字符数组board和单词word，通过邻接的字符连起来组成单词，判断word是否能在board中找到
- 解法：使用dfs

### 212. Word Search II(Medium)

- 题意：给定二维字符数组board和单词数组words，通过邻接的字符连起来组成单词，返回能在board中找到words中的单词列表

- 解法：和上题类似，这里可以使用字典树来避免重复

  ```java
  class Solution {
      public List<String> findWords(char[][] board, String[] words) {
          List<String> res = new ArrayList<>();
          TrieNode root = buildTrie(words);
          for (int i = 0; i < board.length; i++) {
              for (int j = 0; j < board[0].length; j++) {
                  dfs (board, i, j, root, res);
              }
          }
          return res;
      }
  
      public void dfs(char[][] board, int i, int j, TrieNode p, List<String> res) {
          char c = board[i][j];
          if (c == '#' || p.next[c - 'a'] == null) return;
          p = p.next[c - 'a'];
          if (p.word != null) {   // found one
              res.add(p.word);
              p.word = null;     // de-duplicate
          }
  
          board[i][j] = '#';
          if (i > 0) dfs(board, i - 1, j ,p, res); 
          if (j > 0) dfs(board, i, j - 1, p, res);
          if (i < board.length - 1) dfs(board, i + 1, j, p, res); 
          if (j < board[0].length - 1) dfs(board, i, j + 1, p, res); 
          board[i][j] = c;
      }
  
      public TrieNode buildTrie(String[] words) {
          TrieNode root = new TrieNode();
          for (String w : words) {
              TrieNode p = root;
              for (char c : w.toCharArray()) {
                  int i = c - 'a';
                  if (p.next[i] == null) p.next[i] = new TrieNode();
                  p = p.next[i];
             }
             p.word = w;
          }
          return root;
      }
  
      class TrieNode {
          TrieNode[] next = new TrieNode[26];
          String word;
      }
  }
  ```

### 127. Word Ladder(Medium)

- 题意：给定起始词beginWord和终止词endWord和词表wordList，将起始词每次修改一个字符，最后变成终止词，转换中的词都必须在词表里面，求出转换序列的最小长度

- 解法：求最小长度可以使用广度优先遍历，维护一个reached集合表示当前可以得到的词，同时wordDict集合表示当前还没用过的词，求出当前词所能转换出的所有词并判断是否在wordDict中，如果在则从wordDict中移除并加入下一个reached中

  ```java
  class Solution {
      public int ladderLength(String beginWord, String endWord, List<String> wordList) {
          HashSet<String> wordDict = new HashSet<String>();
          for(String word: wordList) {
              wordDict.add(word);
          }
          Set<String> reached = new HashSet<String>();
          reached.add(beginWord);
          int distance = 1;
          while (!reached.contains(endWord)) {
              Set<String> toAdd = new HashSet<String>();
              for (String each: reached) {
                  for (int i = 0; i < each.length(); i++) {
                      char[] chars = each.toCharArray();
                      for (char ch = 'a'; ch <= 'z'; ch++) {
                          chars[i] = ch;
                          String word = new String(chars);
                          if (wordDict.contains(word)) {
                              toAdd.add(word);
                              wordDict.remove(word);
                          }
                      }
                  }
              }
              distance++;
              if (toAdd.size() == 0) return 0;
              reached = toAdd;
          }
          return distance;
      }
  }
  ```

### 390. Elimination Game(Medium)

- 题意：给定n，在1~n中移除数字，从左至右每隔1个移除1个数字，到最右边再从右至左每隔1个移除1个数字，求出最后剩下那个数字

- 解法：只要维护一个开头数字head，根据移除规则更新head，最后剩下的数字一定为head，关键是什么时候更新head

  > When will head be updated?
  >
  > - if we move from left
  > - if we move from right and the total remaining number % 2 == 1
  >   like 2 4 6 8 10, we move from 10, we will take out 10, 6 and 2, head is deleted and move to 4
  >   like 2 4 6 8 10 12, we move from 12, we will take out 12, 8, 4, head is still remaining 2

  ```java
  class Solution {  
      public int lastRemaining(int n) {
          boolean left = true;
          int remaining = n;
          int step = 1;
          int head = 1;
          while (remaining > 1) {
              if (left || remaining % 2 ==1) {
                  head = head + step;
              }
              remaining = remaining / 2;
              step = step * 2;
              left = !left;
          }
          return head;
      }
  }
  ```

### 686. Repeated String Match(Easy)

- 题意：给定字符串A和B，将A重复k次，使B成为A的子串，求最小的k

- 解法：根据A和B的长度来求出总字符串

  ```java
  class Solution {
       public int repeatedStringMatch(String A, String B) {
          int count = 0;
          StringBuilder sb = new StringBuilder();
          while (sb.length() < B.length()) {
              sb.append(A);
              count++;
          }
          if(sb.toString().contains(B)) return count;
          if(sb.append(A).toString().contains(B)) return ++count;
          return -1;
      }
  }
  ```

### 1071. Greatest Common Divisor of Strings(Easy)

- 题意：定义字符串的除法，求两个字符串的最大公约串

- 解法：类似于最大公约数的求法，字符串使用更相减损法更好

  ```java
  class Solution {
      public String gcdOfStrings(String str1, String str2) {
          String longStr = str1.length() < str2.length() ? str2 : str1;
          String shortStr = str1.length() < str2.length() ? str1 : str2;
          String temp = strSubtract(longStr, shortStr);
          while(temp != null && !temp.equals("")) {
              longStr = shortStr.length() < temp.length() ? temp : shortStr;
              shortStr = shortStr.length() < temp.length() ? shortStr : temp;
              temp = strSubtract(longStr, shortStr);
          }
          return temp != null ? shortStr : "";
      }
      
      private String strSubtract(String str1, String str2) {
          for(int i = 0; i < str2.length(); i++) {
              if(str1.charAt(i) != str2.charAt(i)) {
                  return null;
              }
          }
          return str1.substring(str2.length());
      }
  }
  ```

### 134. Gas Station(Medium)

- 题意：有n个加油站组成一个环，起始没油，从第i个加油站开始，加gas[i]油，到第i+1个加油站消耗cost[i]油，求出能到达所有加油站的起始加油站下标，如果不能返回-1

- 解法：gas的和不小于cost的和即有解，找到最大的部分和即可求解

  >找到最小部分和即找到最大部分和
  >
  >gas[0]-cost[0]+gas[1]-cost[1]+...+gas[i]-cost[i]最小，这样可以保证 
  >
  >gas[i+1]-cost[i+1]>=0
  >
  >gas[i+1]-cost[i+1]+gas[i+2]-cost[i+2]>=0
  >
  >…...
  >
  >gas[i+1]-cost[i+1]+gas[i+2]-cost[i+2]+...+gas[n-1]-cost[n-1]>=0

  ```c++
  class Solution {
  public:
      int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
          int n = gas.size();
          int total(0), subsum(INT_MAX), start(0);
          for(int i = 0; i < n; ++i){
              total += gas[i] - cost[i];
              if(total < subsum) {
                  subsum = total;
                  start = i + 1;
              }
          }
          return (total < 0) ?  -1 : (start%n); 
      }
  };
  ```

### 153. Find Minimum in Rotated Sorted Array(Medium)

- 题意：旋转有序数组的某一部分，求出新数组的最小值，类似33题和154题

- 解法：二分法，关键是搞清楚mid和start、end元素的关系

  ```java
  class Solution {
      public int findMin(int[] nums) {
          int start = 0;
          int end = nums.length - 1;
          while(start < end) {
              int mid = (start + end) / 2;
              if(nums[mid] > nums[end]) {
                  start = mid + 1;
              } else {
                  end = mid;
              }
          }
          return nums[end];
      }
  }
  ```

### 154. Find Minimum in Rotated Sorted Array II(Hard)

- 题意：旋转有序数组的某一部分，求出新数组的最小值，数组元素可能重复

- 解法：与153不同的是，如果mid和end元素相等的话，那么无法确定最小元素的位置

  ```c++
  class Solution {
  public:
      int findMin(vector<int> &num) {
          int lo = 0;
          int hi = num.size() - 1;
          int mid = 0;
          
          while(lo < hi) {
              mid = lo + (hi - lo) / 2;
              if (num[mid] > num[hi]) {
                  lo = mid + 1;
              } else if (num[mid] < num[hi]) {
                  hi = mid;
              } else { // when num[mid] and num[hi] are same
                  hi--;
              }
          }
          return num[lo];
      }
  };
  ```

### 915. Partition Array into Disjoint Intervals(Medium)

- 题意：给定数组，将数组拆为两个部分，左边的所有元素不大于右边的所有元素，求出左边元素的最小个数

- 解法：即左边最大元素不大于右边最小元素，使用max和min两个数组记住每个位置对应的最大最小值

  ```java
  class Solution {
      public int partitionDisjoint(int[] A) {
          int[] max = new int[A.length];
          int[] min = new int[A.length];
          max[0] = A[0];
          min[A.length - 1] = A[A.length - 1];
          for(int i = 1; i < A.length; i++) {
              max[i] = Math.max(max[i - 1], A[i]);
          }
          for(int i = A.length - 2; i >= 0; i--) {
              min[i] = Math.min(min[i + 1], A[i]);
          }
          for(int i = 1; i < A.length; i++) {
              if(min[i] >= max[i - 1]) {
                  return i;
              }
          }
          return 0;
      }
  }
  ```

- 解法2：更深地考虑到，如果左边数组中的A[i] > 后面的某一个A[j]，那么这个A[j]一定也属于左边数组，例如32，57，24，19，48，67中32 > 19，所以从32到19的所有元素一定在左边，其中57最大，此时57 > 48，所以48也在左边

  ```java
      public int partitionDisjoint(int[] A) {
          int maxUntilI = A[0];	//当前最大值
          int leftPartitionMax=A[0]; //左边的当前最大值
          int partitionSpot = 0;	//划分点
          for(int i=1;i<A.length;i++){
              maxUntilI =Math.max(maxUntilI, A[i]) ;
              if(A[i]<leftPartitionMax){
                  leftPartitionMax=maxUntilI;
                  partitionSpot =i;
              }            
          }
          return partitionSpot+1;
      }
  ```

### 375. Guess Number Higher or Lower II(Medium)

- 题意：给定数字n，从1-n中选一个数字，每次猜数字i会消耗i代价，返回higher或lower，问在最坏的情况下，最少需要消耗多少代价

- 解法：这是一类问题，最坏情况最优解法，通常使用动态规划+递归来做

  ```java
  class Solution {
      private int[][] memo;
      
      public int getMoneyAmount(int n) {
          memo = new int[n + 1][n + 1];
          return minCost(1, n);
      }
      
      private int minCost(int lo, int hi) {
          if (lo >= hi) return 0;
          
          if (memo[lo][hi] != 0) return memo[lo][hi];
          
          int minTotal = Integer.MAX_VALUE;
          for (int i = lo; i <= hi; i++) {
              minTotal = Math.min(minTotal, i + Math.max(minCost(lo, i - 1), minCost(i + 1, hi)));
          }
          memo[lo][hi] = minTotal;
          return minTotal;
      }
  }
  ```

### 169. Majority Element(Easy)

```java
class Solution {
    //给定数组，求出数组中出现次数大于n/2的元素
    //由于该元素出现次数大于n/2，所以每次遇到该元素就可以将另一个元素抵消掉
    //这样思路就是：记录一个元素，每次遇到另一个不同元素则计数-1，每次遇到相同元素则计数+1，每当计数为0时重新记录一个元素，最后记录的元素一定就是要找的元素
    public int majorityElement(int[] nums) {
        int num = nums[0];
        int count = 1;
        for(int i = 1; i < nums.length; i++) {
            if(num == nums[i]) {
                count++;
            } else if(count == 1) {
                num = nums[++i];
            } else {
                count--;
            }
        }
        return num;
    }
}
```

### 102. Binary Tree Level Order Traversal(Medium)

```java
class Solution {
    //二叉树的层次遍历
    //可以使用队列来保存下一层的元素
    //也可以直接用递归来传递节点的层数
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> levelOrder = new ArrayList<>();
        levelOrderHelper(root, levelOrder, 0);
        return levelOrder;
    }

    public void levelOrderHelper(TreeNode node, List<List<Integer>> levelOrder, int level) {
        if (node == null) {
            return;
        }
        if (level >= levelOrder.size()) {
            levelOrder.add(new ArrayList<Integer>());
        }
        //将该节点加入到对应层的结果中
        levelOrder.get(level).add(node.val);

        levelOrderHelper(node.left, levelOrder, level + 1);
        levelOrderHelper(node.right, levelOrder, level + 1);
    }
}
```

### 287. Find the Duplicate Number(Medium)

```java
class Solution {
    //数组的元素在1-n之间，共n+1个元素，找到数组中的一个重复元素
    //不允许修改数组和申请On辅助空间，并且这个重复数字可能重复多次
    //通过二分法统计区间中数字的个数来判断该重复数字
    //剑指offer上的
    
    //解法2：该题本来可以通过交换数组元素来解决，但是要求不能修改数组，这种方法的本质与数组的索引相关，所以可以通过数组索引链的方法，即当前元素值=下一个索引值
    //问题就转化为了寻找这个链里面的环的起始点
    
    public int findDuplicate(int[] nums) {
        // Find the intersection point of the two runners.
        int tortoise = nums[0];
        int hare = nums[0];
        do {
            tortoise = nums[tortoise];
            hare = nums[nums[hare]];
        } while (tortoise != hare);

        // Find the "entrance" to the cycle.
        //这里ptr2先走了一段环前的路和两个环
        //所以ptr1也要走一段环前的路
        //这样ptr1和ptr2一定会在环起点相遇
        int ptr1 = nums[0];
        int ptr2 = tortoise;
        while (ptr1 != ptr2) {
            ptr1 = nums[ptr1];
            ptr2 = nums[ptr2];
        }
        return ptr1;
    }
}
```

### 324. Wiggle Sort II(Medium)

- 题意：给定无序数组，将其排为`nums[0] < nums[1] > nums[2] < nums[3]...`

- 解法：先划分为大小两部分，然后两边取，要减小空间复杂度就要考虑两边取的下标映射关系

  ```java
  class Solution {
      /*
      方法1， 最简单的做法, 排序，然后两边取
      方法2, 先找到数组的中位数，用类似于215 find largest kth的方法，然后把小于中位数的放左边，大于的放右边，然后两边取，space complexity O(n)
      方法3， 要达到空间复杂度为O(1)，将nums数组的下标x通过函数idx()从[0, 1, 2, ... , n - 1, n] 映射到 [1, 3, 5, ... , 0, 2, 4, ...]，得到新下标ix
      */
      public void wiggleSort(int[] nums) {
          int len = nums.length;
          int[] bak = Arrays.copyOf(nums, len);
          Arrays.sort(bak);
          int mid = (len + 1) / 2;
          int end = len - 1;
          mid--;
  		// corner case  when nums is nums[0:2m]  
  		// smaller :  len(nums[0:m]=m+1, 
  		 //  bigger:  len(nums[m+1:2m]=m
  		//  assigned like nums[m],nums[2m],nums[m-1],nums[2m-1]......  nums[1], nums[m+1] , num[0]
          for (int i = 0; i < len / 2; i++) {
              nums[2 * i] = bak[mid--];
              nums[2 * i + 1] = bak[end--];
          }
          // when len(small) - len (big)=1;
          if (len % 2 != 0) {
              nums[len - 1] = bak[0];
          }
      }
  }
  ```

### 873. Length of Longest Fibonacci Subsequence(Medium)

- 题意：给定单调递增数组，求出数组中斐波那契子数列的最大长度（只要满足前两项之和等于当前项）

- 解法：可以使用集合暴力求解，也可以使用动态规划

  ```java
  class Solution {
      //使用DFS会超时
      //可以使用集合包里搜索A[i]+A[j]
      //也可以用动态规划来做，dp数组表示最大长度，不要想太多，不是floyd算法，这里可以通过索引来确定第三项
      public int lenLongestFibSubseq(int[] A) {
          int res = 0;
          int[][] dp = new int[A.length][A.length];
          Map<Integer, Integer> index = new HashMap<>();
          for (int j = 0; j < A.length; j++) {
              index.put(A[j], j);
              for (int i = 0; i < j; i++) {
                  int k = index.getOrDefault(A[j] - A[i], -1);
                  dp[i][j] = (A[j] - A[i] < A[i] && k >= 0) ? dp[k][i] + 1 : 2;
                  res = Math.max(res, dp[i][j]);
              }
          }
          return res > 2 ? res : 0;
      }
  }
  ```

### 659. Split Array into Consecutive Subsequences(Medium)

- 题意：将数组拆分为连续的子序列

- 解法：使用两个哈希表（也可以约简为空间复杂度O(1)的算法）：

  哈希表 counter 用于存储元素出现的次数，counter[n] 代表 n 出现的次数
  哈希表 end 用于存储以元素结尾的连续子序列（指至少包含三个连续整数的子序列）个数，end[n] 代表以 n 结尾的连续子序列的个数
  过程如下：

  > 遍历数组 nums：
  >
  > ​	若元素 n 的出现次数 count[n] == 0：跳过该元素
  >
  > ​	count[n] -= 1
  >
  > ​	若元素 n 的出现次数 count[n] > 0：
  > ​		存在以元素 n - 1 结尾的连续子序列，即 end[n - 1] > 0，将元素添加到该子序列的末尾，操作数据：
  > ​			以 n - 1 结尾的子序列数量减 1：end[n - 1] -= 1
  > ​			以 n 结尾的子序列数量加 1：end[n] += 1
  > ​		不存在以元素 n - 1 结尾的连续子序列，此时判断是否能以 n 作为开头构建连续子序列，即判断 counter[n + 1] 与 counter[n + 2] 的值是否均大于 0：
  > ​			若不能构成：返回 False
  > ​			若可以构成，操作数据：
  > ​				n + 1 元素数量减 1：counter[n + 1] -= 1
  > ​				n + 2 元素数量减 1：counter[n + 2] -= 1
  > ​				以 n + 2 元素结尾的子序列数量加 1：end[n + 2] += 1

```java
class Solution {
    public boolean isPossible(int[] nums) {
        int start = nums[0];
        int end = nums[nums.length - 1];
        int len = end - start + 1;
        int[] count = new int[len];
        for(int num: nums) {
            count[num - start]++;
        }
        int[] ends = new int[len];
        for(int i: nums) {
            int x = i - start;
            if(count[x] == 0) continue;
            count[x]--;
            if(x > 0 && ends[x-1] > 0) {
                ends[x-1]--;
                ends[x]++;
            } else if((x < len - 2) && count[x+1] > 0 && count[x+2] > 0) {
                count[x+1]--;
                count[x+2]--;
                ends[x+2]++;
            } else {
                return false;
            }
        }
        return true;
    }
}
```

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

### 10. ZigZag Conversion(Medium)

- 题意：求出字符串未重复字母的最长子串
- 解法：使用滑动窗口求解

# END