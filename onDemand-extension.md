## A default user need a security rule like:  ##

Name of the rule: "SER SharedCintente User"
...
Actions: "create, read, update, delete"
...
Conditiosn: "!user.IsAnonymous()"


