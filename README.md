<<<<<<< HEAD
# Strategy

1.求**子串**或者**平台（子数组）**等连续子集问题或者**有序数字对（需要先排序）**一般可以使用滑动窗口，如[第3题无重复字母最长子串](https://leetcode.com/problems/longest-substring-without-repeating-characters/)和[第424题最长重复字符替换子串](https://leetcode.com/problems/longest-repeating-character-replacement/)和[532题距离为k的数字对](https://leetcode.com/problems/k-diff-pairs-in-an-array/)

2.遇到可以拆分为子问题的题目，一般递归都可以解决，更好的做法是使用动态规划，如[5题最长回文子串](https://leetcode.com/problems/longest-palindromic-substring/)，[10题正则表达式](https://leetcode.com/problems/regular-expression-matching/)和[808题取汤](<https://leetcode.com/problems/soup-servings/>)(动态规划递归解决方案)

3.**数组的前后**有顺序关系，一般都可以使用首尾指针来解决，保持有序还可以保证没有重复（如果输出是数组的话），如[11题面积最大的平台](https://leetcode.com/problems/container-with-most-water/)和[15题三数之和](https://leetcode.com/problems/3sum/)，还有一些题比较隐晦，如[462题求使数组元素都相等的最小操作次数](https://leetcode.com/problems/minimum-moves-to-equal-array-elements-ii/)，首尾指针同步更新，求出指针的距离和

4.使用**优先队列**可以保证时间复杂度为O(logn)的插入删除操作，常用在**排序**中，如[23题合并k个有序链表](https://leetcode.com/problems/merge-k-sorted-lists/)

5.遇到**整型乘除**可以转化为二进制和移位运算来处理，但要注意是否溢出，如[29题实现整数的除法](https://leetcode.com/problems/divide-two-integers/)

6.回溯法的典型例子，可用于求所有子集，序列，子串等问题，如[39题凑出目标和](https://leetcode.com/problems/combination-sum/)，[参考](https://leetcode.com/problems/combination-sum/discuss/16502/A-general-approach-to-backtracking-questions-in-Java-(Subsets-Permutations-Combination-Sum-Palindrome-Partitioning))

7.**有重复元素的数组**都可以这么处理：先排序，然后通过判断相等的前一元素是否使用过，来判断当前元素是否可用，保证了使用顺序，使结果独特，如[47题数组所有独特排列](https://leetcode.com/problems/permutations-ii/)；如果是求**子数组**的话，那么**顺序不能改变**，需要在回溯函数内使用set来判断元素是否已经考虑过，以此来保证不重复，如[491题不降序子数组](https://leetcode.com/problems/increasing-subsequences/)

8.**跳跃问题**考虑层次遍历的思想，如[55题跳跃游戏](https://leetcode.com/problems/jump-game/)

9.java中int数组初始化后默认的值为0，而Integer等引用数据类型的默认值为null，在动态规划算法中可以利用这点

10.求最值问题，如果与顺序相关，那么可以换种思路：遍历求出最值+动态规划(**分治**)，如[312题扎气球](https://leetcode.com/problems/burst-balloons/)和[375题猜数字](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)

11.给定了固定的n个整数，要将其按照某种顺序排好，可以考虑使用索引形成环，然后环中元素交换来分解环，并以此来排序，如[765题数字相邻](<https://leetcode.com/problems/couples-holding-hands/>)和[数学归纳法证明](<https://leetcode.com/problems/couples-holding-hands/discuss/113362/JavaC%2B%2B-O(N)-solution-using-cyclic-swapping>)。更一般的，如果数组的元素与数组的索引相关（比如数组长为n且数组元素都在1-n之间），可以通过数组索引链的方法来做（即当前元素值=下一个索引值），并且可以联想到链表环的快慢指针法，如[287找重复元素](https://leetcode.com/problems/find-the-duplicate-number/)

12.**二叉搜索树在中序遍历下有顺序**，可以直接当做一个有序序列来看待，如[501题找二叉搜索树的众数](<https://leetcode.com/problems/find-mode-in-binary-search-tree/>)，很多关于二叉搜索树的问题都可以变成**有序数组**的问题，如[99题修复二叉搜索树](https://leetcode.com/problems/recover-binary-search-tree/)

13.遇到数学问题相关的一定要先推导出公式再动手编码，如[829题连续正整数和](<https://leetcode.com/problems/consecutive-numbers-sum/solution/>)

14.广度优先搜索可以保证***最小长度***，用在地图着色、倒水等问题，比如[854题求字符串的相似度](<https://leetcode.com/problems/k-similar-strings/>)。如果与广度的顺序无关，只有最终结果有关的话，也可以不用队列而用递归来实现，只需要将层数当做参数，如[102二叉树层次遍历](https://leetcode.com/problems/binary-tree-level-order-traversal/)

15.动态规划：最值问题，尽可能接近，匹配问题，一般都是将题目中提到的东西当做下标

回溯法：求出所有排列，组合

分治法：最值问题，可以分解为子问题

指针法：交换问题，两数问题，子串、子数组(连续)

移位运算：加减乘除问题，异或运算可以用来实现查重(注意异或交换元素时两个元素都指向同一个地址的情况)，n = n & (n-1)可以把n二进制最右边的1置为0

二进制与或非：关于***二进制***比特的题

16.统计符合某种规律的序列的个数一般可以用dfs或者bfs来做，dfs使用递归，bfs则还需要一个队列，为了保证不重复，可以使用used布尔数组，也可以使用元素交换(固定数组，更快)，如[526求数字与下标有关的所有排列数](<https://leetcode.com/problems/beautiful-arrangement/>)

17.如果要求的最值只与数组中两个元素的位置有关，与其他元素无关，可以先求出第一个元素的所有可能位置，可以使用栈来存储，因为这些可能元素往往会有顺序比如大小关系，然后贪心遍历第二个元素位置即可，如[962题求两元素最大宽度](<https://leetcode.com/problems/maximum-width-ramp/>)

18.关于多个连通图或者多棵树的问题可以使用并查集来解决，特别是当给出的并不是整个图而是图中元素坐标时，如[947题数岛屿问题](<https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/>)

19.如果要求的是子序列问题，即保证相对顺序，可以使用栈来存储，因为栈有顺序性，如[316题求最小字典序独特字符子序列](https://leetcode.com/problems/remove-duplicate-letters/)

20.关于链表的题可以考虑使用快慢指针来做，如[876求链表的中间元素](https://leetcode.com/problems/middle-of-the-linked-list/)

21.如果需要对一个二元组排序，可以考虑将二元编码为一元，然后排序，如char、int可以编码为(char - 'a') + int * 100

22.多个字符串的查找匹配可以使用字典树来减少重复查找，如[212单词查找](https://leetcode.com/problems/word-search-ii/)

23.数字low-high之间的平均数是low+(high-low)/2，而不是(high+low)/2，因为后者可能会溢出

24.遇到一类问题，求最坏情况的最优解法，可以使用动态规划来保存局部最优解，同时使用递归来求动态规划数组（思路更清晰），如[375猜数字的代价](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)

=======
# Strategy

1.求**子串**或者**平台（子数组）**等连续子集问题或者**有序数字对（需要先排序）**一般可以使用滑动窗口，如[第3题无重复字母最长子串](https://leetcode.com/problems/longest-substring-without-repeating-characters/)和[第424题最长重复字符替换子串](https://leetcode.com/problems/longest-repeating-character-replacement/)和[532题距离为k的数字对](https://leetcode.com/problems/k-diff-pairs-in-an-array/)

2.遇到可以拆分为子问题的题目，一般递归都可以解决，更好的做法是使用动态规划，如[5题最长回文子串](https://leetcode.com/problems/longest-palindromic-substring/)，[10题正则表达式](https://leetcode.com/problems/regular-expression-matching/)和[808题取汤](<https://leetcode.com/problems/soup-servings/>)(动态规划递归解决方案)

3.**数组的前后**有顺序关系，一般都可以使用首尾指针来解决，保持有序还可以保证没有重复（如果输出是数组的话），如[11题面积最大的平台](https://leetcode.com/problems/container-with-most-water/)和[15题三数之和](https://leetcode.com/problems/3sum/)，还有一些题比较隐晦，如[462题求使数组元素都相等的最小操作次数](https://leetcode.com/problems/minimum-moves-to-equal-array-elements-ii/)，首尾指针同步更新，求出指针的距离和

4.使用**优先队列**可以保证时间复杂度为O(logn)的插入删除操作，常用在**排序**中，如[23题合并k个有序链表](https://leetcode.com/problems/merge-k-sorted-lists/)

5.遇到**整型乘除**可以转化为二进制和移位运算来处理，但要注意是否溢出，如[29题实现整数的除法](https://leetcode.com/problems/divide-two-integers/)

6.回溯法的典型例子，可用于求所有子集，序列，子串等问题，如[39题凑出目标和](https://leetcode.com/problems/combination-sum/)，[参考](https://leetcode.com/problems/combination-sum/discuss/16502/A-general-approach-to-backtracking-questions-in-Java-(Subsets-Permutations-Combination-Sum-Palindrome-Partitioning))

7.**有重复元素的数组**都可以这么处理：先排序，然后通过判断相等的前一元素是否使用过，来判断当前元素是否可用，保证了使用顺序，使结果独特，如[47题数组所有独特排列](https://leetcode.com/problems/permutations-ii/)；如果是求**子数组**的话，那么**顺序不能改变**，需要在回溯函数内使用set来判断元素是否已经考虑过，以此来保证不重复，如[491题不降序子数组](https://leetcode.com/problems/increasing-subsequences/)

8.**跳跃问题**考虑层次遍历的思想，如[55题跳跃游戏](https://leetcode.com/problems/jump-game/)

9.java中int数组初始化后默认的值为0，而Integer等引用数据类型的默认值为null，在动态规划算法中可以利用这点

10.求最值问题，如果与顺序相关，那么可以换种思路：遍历求出最值+动态规划(**分治**)，如[312题扎气球](https://leetcode.com/problems/burst-balloons/)和[375题猜数字](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)

11.给定了固定的n个整数，要将其按照某种顺序排好，可以考虑使用索引形成环，然后环中元素交换来分解环，并以此来排序，如[765题数字相邻](<https://leetcode.com/problems/couples-holding-hands/>)和[数学归纳法证明](<https://leetcode.com/problems/couples-holding-hands/discuss/113362/JavaC%2B%2B-O(N)-solution-using-cyclic-swapping>)。更一般的，如果数组的元素与数组的索引相关（比如数组长为n且数组元素都在1-n之间），可以通过数组索引链的方法来做（即当前元素值=下一个索引值），并且可以联想到链表环的快慢指针法，如[287找重复元素](https://leetcode.com/problems/find-the-duplicate-number/)

12.**二叉搜索树在中序遍历下有顺序**，可以直接当做一个有序序列来看待，如[501题找二叉搜索树的众数](<https://leetcode.com/problems/find-mode-in-binary-search-tree/>)，很多关于二叉搜索树的问题都可以变成**有序数组**的问题，如[99题修复二叉搜索树](https://leetcode.com/problems/recover-binary-search-tree/)

13.遇到数学问题相关的一定要先推导出公式再动手编码，如[829题连续正整数和](<https://leetcode.com/problems/consecutive-numbers-sum/solution/>)

14.广度优先搜索可以保证***最小长度***，用在地图着色、倒水等问题，比如[854题求字符串的相似度](<https://leetcode.com/problems/k-similar-strings/>)。如果与广度的顺序无关，只有最终结果有关的话，也可以不用队列而用递归来实现，只需要将层数当做参数，如[102二叉树层次遍历](https://leetcode.com/problems/binary-tree-level-order-traversal/)

15.动态规划：最值问题，尽可能接近，匹配问题，一般都是将题目中提到的东西当做下标

回溯法：求出所有排列，组合

分治法：最值问题，可以分解为子问题

指针法：交换问题，两数问题，子串、子数组(连续)

移位运算：加减乘除问题，异或运算可以用来实现查重(注意异或交换元素时两个元素都指向同一个地址的情况)，n = n & (n-1)可以把n二进制最右边的1置为0

二进制与或非：关于***二进制***比特的题

16.统计符合某种规律的序列的个数一般可以用dfs或者bfs来做，dfs使用递归，bfs则还需要一个队列，为了保证不重复，可以使用used布尔数组，也可以使用元素交换(固定数组，更快)，如[526求数字与下标有关的所有排列数](<https://leetcode.com/problems/beautiful-arrangement/>)

17.如果要求的最值只与数组中两个元素的位置有关，与其他元素无关，可以先求出第一个元素的所有可能位置，可以使用栈来存储，因为这些可能元素往往会有顺序比如大小关系，然后贪心遍历第二个元素位置即可，如[962题求两元素最大宽度](<https://leetcode.com/problems/maximum-width-ramp/>)

18.关于多个连通图或者多棵树的问题可以使用并查集来解决，特别是当给出的并不是整个图而是图中元素坐标时，如[947题数岛屿问题](<https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/>)

19.如果要求的是子序列问题，即保证相对顺序，可以使用栈来存储，因为栈有顺序性，如[316题求最小字典序独特字符子序列](https://leetcode.com/problems/remove-duplicate-letters/)

20.关于链表的题可以考虑使用快慢指针来做，如[876求链表的中间元素](https://leetcode.com/problems/middle-of-the-linked-list/)

21.如果需要对一个二元组排序，可以考虑将二元编码为一元，然后排序，如char、int可以编码为(char - 'a') + int * 100

22.多个字符串的查找匹配可以使用字典树来减少重复查找，如[212单词查找](https://leetcode.com/problems/word-search-ii/)

23.数字low-high之间的平均数是low+(high-low)/2，而不是(high+low)/2，因为后者可能会溢出

24.遇到一类问题，求最坏情况的最优解法，可以使用动态规划来保存局部最优解，同时使用递归来求动态规划数组（思路更清晰），如[375猜数字的代价](https://leetcode.com/problems/guess-number-higher-or-lower-ii/)

25.