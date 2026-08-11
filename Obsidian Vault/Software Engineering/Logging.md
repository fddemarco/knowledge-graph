**The best logs:**
- Tell you exactly what happened: when, where, how
- Are suitable for manual, semi-automated, and automated analysis
- Can be analyzed without the application that produced them being to hand
- Don’t slow the system down
- Can be proven as reliable if used as evidence
**Events To Log**
- Authentication/authorization decisions (including logoff)
- System access, data access
- System/application changes (especially privilege changes)
- Data changes: add/edit/delete
- Invalid input (possible badness/threats)
- Resources (RAM, Disk, CPU, Bandwidth, any other hard or soft limits)
- Health/availability: startups/shutdowns, faults/errors, delays, backups success/failure
**What To Log – Every Event Should Have:**
- Timestamp & timezone (when)
- System, application, or component (where); IP’s and contemporaneous DNS lookups of involved parties; names/roles of systems involved (what servers are we talking to?), name/role of local application (what is this server?)
- User (who)
- Action (what)
- Status (result)
- Priority (severity, importance, rank, level, etc)
- Reason
