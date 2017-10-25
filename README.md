Repro for MNG-4416 or something near.
@author kostd@Argus

0. License.
This software is absolutely free. U can do whatever you want with`em (but do no harm to others).

1. Steps to reproduce:

1.1. cd to mng-4416-reproducer/server-app/env-modules/child-with-profile-disabled/
1.2. mvn initialize:
```
[INFO] ------------------------------------------------------------------------
[INFO] Building child-with-profile-disabled 3.20.0
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-antrun-plugin:1.3:run (first-execution-in-grandparent-pom) @ child-with-profile-disabled ---
[INFO] Executing tasks
     [echo] first-execution-in-grandparent-pom
[INFO] Executed tasks
[INFO] 
[INFO] --- maven-antrun-plugin:1.3:run (second-execution-in-grandparent-pom) @ child-with-profile-disabled ---
[INFO] Executing tasks
     [echo] second-execution-in-grandparent-pom
[INFO] Executed tasks
[INFO] 
[INFO] --- gmaven-plugin:1.5-jenkins-3:execute (single-execution-in-parent-pom) @ child-with-profile-disabled ---
single-execution-in-parent-pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```
U can see expected plugin execution ordering: grandparent executions first, parent executions after.

1.3. cd to mng-4416-reproducer/server-app/env-modules/child-with-profile-enabled/
1.4. mvn initialize:
```
[INFO] ------------------------------------------------------------------------
[INFO] Building child-with-profile-enabled 3.20.0
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- gmaven-plugin:1.5-jenkins-3:execute (single-execution-in-parent-pom) @ child-with-profile-enabled ---
single-execution-in-parent-pom
[INFO] 
[INFO] --- maven-antrun-plugin:1.3:run (first-execution-in-grandparent-pom) @ child-with-profile-enabled ---
[INFO] Executing tasks
     [echo] first-execution-in-grandparent-pom
[INFO] Executed tasks
[INFO] 
[INFO] --- maven-antrun-plugin:1.3:run (second-execution-in-grandparent-pom) @ child-with-profile-enabled ---
[INFO] Executing tasks
     [echo] second-execution-in-grandparent-pom
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```
As you see this is affected execution order: parent first, grandparent after. It doesn`t metter, what grandparent-parent
order consider to correct, but order _must not_ be changed when profile activated!


2. Reproduced on environment:
-- OS name: "windows 7", version: "6.1", arch: "amd64", family: "dos"
-- Default locale: ru_RU, platform encoding: Cp1251
-- Java version: 1.8.0_77, vendor: Oracle Corporation
-- Apache Maven 3.3.1 (cab6659f9874fa96462afef40fcf6bc033d58c1c; 2015-03-13T23:10:27+03:00)

3. Special conditions to reproduce:

3.1. plugin in test-profile (parent/pom.xml) must be the same as plugin defined before affected executions(grandparent/pom.xml). 
That is, in our case maven-compiler-plugin and maven-compiler-plugin

3.2. affected plugins must have different types: maven-antrun-plugin in grandparent and gmaven-plugin in parent. Tried
with gmaven-plugin on grandparent -- cant reporduce.

3.3. depends on directory structure. Tried in flat structure -- cant reproduce.


P.S. This repro is simplified _real_ case from our big project. This case is not sinthetic, repair is important for us.
