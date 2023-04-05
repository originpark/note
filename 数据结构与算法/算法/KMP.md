**KMP描述**

KMP是一种查找子串的算法，相对于暴力查找，其主串指针不回溯，只有子串指针回溯，并且字串指针每次并不是回溯到0，而是回溯到一个特定的位置，将这些位置存放在一个叫做next的数组中，即next[i]的值即为当子串在i位置匹配失败后回溯的下标值，因此kmp的重点是如何求得next数组。





**举例**

主串：a  b  c  a  b  a  a  b  c  a  b  c

子串：a  b  c  a  b  c



一开始与暴力匹配相同，主串指针i和子串指针j分别指向第一个字符，然后逐一比较，相同就后移，当i和j等于5时发现匹配失败，此时观察，主串中匹配失败的字符a前面有一个前缀ab，而我们在子串的开头也找到了一个前缀ab，我们可以假设此时正在将主串第四个位置开始的串与子串进行匹配，并且已经匹配到了第三个字符，因此我们只需要将子串指针回溯到2下标处(即next[5] = 2), 继续进行比较即可，直到j遍历完子串说明查找成功，如果i遍历完主串时j仍未遍历完子串，说明未找到。



我们在求子串的next数组时是不需要依靠主串的，因此需要将上面的思路稍微转变一下，由于主串和子串已经匹配过的部分是完全相同的，因此可以将在主串中找前缀改成在子串的对应位置寻找，此时求next数组就转为了在子串中寻找相同的前后缀。



在代码实现中，规定next[0] = -1, next[1] = 0，从第三个下标为i的字符开始，k为前一个字符的next下标，如果sub[i - 1] = sub[k]，则有sub[i] = k + 1，如果sub[i - 1] != sub[k]，则继续回溯，直到发现sub[i - 1] = sub[k]或者k变为-1



```java
public static int KMP(String str, String sub, int pos) {
    if (str == null || sub == null) return -1;
    int lenStr = str.length(), lenSub = sub.length();
    if (lenStr == 0 || lenSub == 0 || lenSub > lenStr) return -1;
    if (pos > lenStr - 1 || lenStr - 1 - pos < sub.length()) return -1;

    int[] next = new int[lenSub];
    getNext(sub, next);//求子串的next数组
    int i = pos;
    int j = 0;
    while (i < lenStr && j < lenSub) {
        if (j == -1 || str.charAt(i) == sub.charAt(j)) {
            i++;
            j++;
        } else {
            j = next[j];//子串指针回溯
        }
    }
    if(j >= lenSub) {
        return i - lenSub;
    } else {
        return -1;
    }
}

public static void getNext(String sub, int[] next) {
    next[0] = -1;
    next[1] = 0;
    for(int i = 2, k = 0; i < sub.length();) {
        if(k == -1 || sub.charAt(i - 1) == sub.charAt(k)) {
            next[i] = k + 1;
            /**
            该部分是对next数组的优化，即如果当前字符要回溯的位置上的字符与当前字符仍然相同，那么说明肯定还要继续			回溯，不如干脆一步回溯到位，即将当前字符的next值设置要回溯的位置上的字符的next值
            if(sub.charAt(i) == sub.charAt(next[i])) {
                    next[i] = next[next[i]];
                }
            */
            k = next[i];
            i++;
        } else {
            k = next[k];
        }
    }
}
```



