SigninLogs
| where TimeGenerated > ago(1h)
| where ResultType != "0" // 0 = success
| summarize
FailedAttempts = count(),
DistinctIPs = dcount(IPAddress),
IPList = make_set(IPAddress),
FirstSeen = min(TimeGenerated),
LastSeen = max(TimeGenerated)
by UserPrincipalName, bin(TimeGenerated, 5m)
| where FailedAttempts > 10
| extend TimeDelta = LastSeen - FirstSeen
| project
UserPrincipalName, FailedAttempts,
DistinctIPs, IPList, FirstSeen, LastSeen, TimeDelta
| order by FailedAttempts desc
