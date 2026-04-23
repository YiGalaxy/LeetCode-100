# LeetCode一百题解析

这些都是我个人的理解和经验，毕竟我也是个新手，做这些都是为了更好的理解，如果大家发现有什么问题，欢迎指出改正，谢谢大家

## 哈希

### 1.两数之和

####题目

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`* 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案，并且你不能使用两次相同的元素。

你可以按任意顺序返回答案。

**示例 1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**示例 2：**

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例 3：**

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

 

**提示：**

- `2 <= nums.length <= 104`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`
- **只会存在一个有效答案**

 

**进阶：**你可以想出一个时间复杂度小于 `O(n2)` 的算法吗？

####解析

这道题目的是从给你的一个数组里面找到两个数，这两个数的和要等于目标值。

这样就很清晰我们的思路了

我们可以遍历数组，将遍历的每一个值都与目标值做差，得到的这个值，如果存在于数组中，那么这两个数就满足题目的要求。

下面使用C++实现

```c++
vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (nums[i] + nums[j] == target) {
                    return {i, j};
                }
            }
        }
        return {};
    }
```

这个解法是典型的暴力循环解法，能解出来，但是时间复杂度为O(n^2^)。

但这个仍然有优化空间，就是使用哈希表

```c++
vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> map;  // key: 数值, value: 下标
        
        for (int i = 0; i < nums.size(); i++) {
            int need = target - nums[i];  // 需要找的另一个数
            
            // 如果哈希表里已经有这个数，直接返回结果
            if (map.find(need) != map.end()) {
                return {map[need], i};
            }
            
            // 没有就把当前数和下标存进哈希表
            map[nums[i]] = i;
        }
        
        return {};  // 题目保证有解，这里不会执行
    }

```

使用哈希表，可以将时间复杂度从**O(n^2^**)降到**O(n)**

这也说明了哈希表是一个对于算法来说很重要的东西，要重点掌握其使用方式，接下来，我们先讲一下这个方法下的代码。

首先，使用**unordered_map<int, int> hash**来生成定义一个哈希表，**<>**里的两个参数分别代表的是**key（键）**的类型和**value(值)**的类型，就如同这里，使用的是**int**类型的**key**和**value**，因为我们要存放的对象是数组的下标和对应的值。

然后我们遍历数组对遍历的每一个值进行计算，算出另外一个符合的值，然后在哈希表里面查询这个值是否存在。如果有就返回，没有就继续找。

这里说一下这个地方

```c++
 if (map.find(need) != map.end()) {
                return {map[need], i};
            }
```

**map.find(key)**是根据key值来查找的，返回的是一个迭代器，是一个指向**{key, value}** 的一个 “指针”，不仅可以像如上代码使用**map[need]**来取出这个key下对应的value，也可以采用如下办法，

```c++
auto it = map.find(2);
// 拿到 key
cout << it->first;   // 输出 2
// 拿到 value
cout << it->second;  // 输出 0
```

如果没找到的话，它就会返回**map.end()**,这实际就是一个哈希表结束的标记，如果没找到，**map.find**()就会返回这个标记，然后和**map.end()**相互比较，如果相等就是没找到，不等就是找到了。

为什么时间复杂度会降低到**O(n)**呢，这是就是因为哈希表的特性了，在哈希表里面找东西，不是用遍历的查找，而是直接锁定到目标位置，而这个直接锁定是怎么实现的呢，是算出来的，这里就不多提及了，感兴趣的可以去单独看看。

###2.字母异位词分组

####题目

给你一个字符串数组，请你将 字母异位词 组合在一起。可以按任意顺序返回结果列表。

**示例 1:**

**输入:** strs = ["eat", "tea", "tan", "ate", "nat", "bat"]

**输出:** [["bat"],["nat","tan"],["ate","eat","tea"]]

**解释：**

- 在 strs 中没有字符串可以通过重新排列来形成 `"bat"`。
- 字符串 `"nat"` 和 `"tan"` 是字母异位词，因为它们可以重新排列以形成彼此。
- 字符串 `"ate"` ，`"eat"` 和 `"tea"` 是字母异位词，因为它们可以重新排列以形成彼此。

**示例 2:**

**输入:** strs = [""]

**输出:** [[""]]

**示例 3:**

**输入:** strs = ["a"]

**输出:** [["a"]]

 

**提示：**

- `1 <= strs.length <= 104`
- `0 <= strs[i].length <= 100`
- `strs[i]` 仅包含小写字母

#### 解析

还是一样，我们先来理解字母异位词这个概念。

就是说，这个字符串数组里面的这些字符串，能够通过任意的组合形成彼此的字符串样式，例如“abc”，“cba”，这两个字符串都可以通过变换变成彼此，然后满足这个需求的就可以分为一组。

说人话，就是拥有相同的字母类型和对应个数的字符串就可以分为一组，例如两个字符串里面拥有相同数量的a，相同数量的b，相同数量的c，那么就可以认为这是满足的。

所以我们就有了一个基本思路，对每个字符串进行排序，这样就可以将满足的字符串变成长得一模一样的字符串，然后将满足的字符串下标进行分组，再将这些重新输出为二维数组就可以了。

下面使用C++进行实现：

```c++
vector<vector<string>> groupAnagrams(vector<string>& strs) {
    //这个地方我们先创建两个动态数组，一个用来存放我们返回的结果，一个存放目标字符串是否已经被分组的布尔值
    vector<vector<string>> res;
    vector<bool> used(strs.size(), false);
	//这里开始进行遍历
    for (int i = 0; i < strs.size(); i++) {
        if (used[i]) continue;//这里意思是如果已经使用了就直接跳过，不再进行无用的遍历
		
        //这里创建一个动态的字符串数组来分组，满足的就分到这一组里面
        vector<string> group;
        group.push_back(strs[i]);
        used[i] = true;//标记为已使用

        // 拿到当前字符串排序
        string key_i = strs[i];
        sort(key_i.begin(), key_i.end());

        // 遍历后面所有字符串
        for (int j = i + 1; j < strs.size(); j++) {
            if (used[j]) continue;

            string key_j = strs[j];
            sort(key_j.begin(), key_j.end());

            // 如果排序后一样 → 同一组
            if (key_i == key_j) {
                group.push_back(strs[j]);
                used[j] = true;
            }
        }
        res.push_back(group);
    }
    return res;
}
```

这个仍然采用的是暴力的方式，在我看来，只要你会写暴力的解，那么在其上进行优化也不会太难了。

这个暴力的解法你在LeetCode上提交的话会执行不通过，因为时间超了，时间复杂度为**O (n^2^ × k log k)**，所以我们就可以采取优化，这里我们也可以发现和上一题一样的使用双for来循环对照，所以我们这个题也可以用哈希表来将第二个for给优化了。

下面我们就来使用哈希表来解决

```c++
vector<vector<string>> groupAnagrams(vector<string>& strs) {
        
        // 哈希表
        // key: 排序后的字符串
        // value: 同一组的所有原字符串
        unordered_map<string, vector<string>> map;

        // 1. 遍历一次所有字符串 O(n)
        for (string s : strs) {//这里的用法就是遍历strs数组，再把里面的每一个放到s
            // 2. 排序生成 key
            string key = s;
            sort(key.begin(), key.end());
            
            // 3. 直接放进哈希表对应组里 O(1)
            map[key].push_back(s);
        }

        // 4. 把哈希表的结果转成二维数组输出
        vector<vector<string>> res;
        for (auto& entry : map) {//这里auto就是自动分类型，map里是啥就是啥类型，然后&就是取引用嘛，会比复制一份出来快一些
            res.push_back(entry.second);
        }

        return res;
    }
```

优化完后，时间复杂度也降低到了**O(n × k log k)**

同时我们也发现了代码也会少一些，这就是算法优化的好处