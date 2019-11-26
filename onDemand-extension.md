## A default user need a security rule like:  ##

Name of the rule: "SER SharedClient User"
...
Actions: "create, read, update, delete"
...
Conditions: "!user.IsAnonymous()"


