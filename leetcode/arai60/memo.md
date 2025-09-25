# 252. Meeting Rooms
- 問題: https://leetcode.com/problems/meeting-rooms
  - Premium問題のためNeetCodeを利用した: https://neetcode.io/problems/meeting-schedule
- 言語: Python

## Step1
- 前の会議終了時刻と次の会議開始時刻を比較して、前の会議終了時刻 > 次の会議開始時刻であればFalseを返す
- `intervals` はソートされていないので、安定ソートで会議開始時刻でソートする

### 解答（AC）
```py
class Solution:
    def canAttendMeetings(self, intervals: List[Interval]) -> bool:
        sorted_intervals = sorted(intervals, key=lambda interval: interval.start)
        previous_interval_end = -1
        for interval in sorted_intervals:
            if previous_interval_end > interval.start:
                return False
            previous_interval_end = interval.end
        
        return True
```
- 解答時間: 10:22
- `for interval in sorted_intervals:` は分割代入した方が読みやすかったかも

## Step2
- 典型コメント集: なし
- https://github.com/potrue/leetcode/pull/55
  - C++
  - `(開始時刻, True)` 、 `(終了時刻, False)` の組でリストを作成
    - 2番目の真偽値は、会議に出席しているかどうかの状態を表す
  - 上のリストを時刻でソート
  - 変数名 `attending` は意味論として分かりやすいと思った
- https://github.com/tokuhirat/LeetCode/pull/55
  - Python
  - > 終了時刻が早い順に見ていき、それより早く開始する会議があれば参加できない
  - 終了時刻でソートしているところ以外は同じ
  - 参加可能な会議数を求めるなどの汎用性が出る
- https://github.com/olsen-blue/Arai60/pull/56
  - Python
  - 累積和の問題として解釈

### 読みやすく直したコード
```py
class Solution:
    def canAttendMeetings(self, intervals: List[Interval]) -> bool:
        sorted_intervals = sorted(intervals, key=lambda x: x.end)
        last_meeting_end = -1
        for interval in sorted_intervals:
            if interval.start < last_meeting_end:
                return False
            last_meeting_end = interval.end
        
        return True
```

## Step3
```py
class Solution:
    def canAttendMeetings(self, intervals: List[Interval]) -> bool:
        sorted_intervals = sorted(intervals, key=lambda x: x.end)
        last_meeting_end = -1
        for interval in sorted_intervals:
            if interval.start < last_meeting_end:
                return False
            last_meeting_end = interval.end
        
        return True
```
- 解答時間: 
  - 1回目: 2:00
  - 2回目: 1:47
  - 3回目: 1:38
