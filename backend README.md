[1mdiff --git a/README.md b/README.md[m
[1mindex 7a0e521..8e3807d 100644[m
[1m--- a/README.md[m
[1m+++ b/README.md[m
[36m@@ -1,4 +1,12 @@[m
 # cloud_labs[m
[31m-Starter application used across the Cloud Computing and Cloud Security [m
[31m-labs. [m
[31m-## Lab 1 - Development environment verified - Static HTML structure created - CSS interface created - Git repository prepared [m
\ No newline at end of file[m
[32m+[m
[32m+[m[32mStarter application used across the Cloud Computing and Cloud Security[m
[32m+[m[32mlabs.[m
[32m+[m
[32m+[m[32m## Lab 1 - Development environment verified - Static HTML structure created - CSS interface created - Git repository prepared[m
[32m+[m
[32m+[m[32m## Lab 3 — Application and API Design[m
[32m+[m
[32m+[m[32mThe Cloud Issue Tracker now has a documented three-layer architecture and a REST API contract for create, view and status[m
[32m+[m[32mchange operations. The backend project is initialised and its data service reads and writes issues in backend/data/issues.json. See the[m
[32m+[m[32mfiles in /docs before implementing the API routes.[m
[1mdiff --git a/backend/README.md b/backend/README.md[m
[1mdeleted file mode 100644[m
[1mindex 7a0e521..0000000[m
[1m--- a/backend/README.md[m
[1m+++ /dev/null[m
[36m@@ -1,4 +0,0 @@[m
[31m-# cloud_labs[m
[31m-Starter application used across the Cloud Computing and Cloud Security [m
[31m-labs. [m
[31m-## Lab 1 - Development environment verified - Static HTML structure created - CSS interface created - Git repository prepared [m
\ No newline at end of file[m
[1mdiff --git a/frontend/package.json b/frontend/package.json[m
[1mindex 874883e..c8cbeec 100644[m
[1m--- a/frontend/package.json[m
[1m+++ b/frontend/package.json[m
[36m@@ -4,13 +4,14 @@[m
   "description": "",[m
   "main": "index.js",[m
   "scripts": {[m
[31m-    "start": "http-server . -p 8080" [m
[32m+[m[32m    "start": "node server.js",[m
[32m+[m[32m    "dev": "node --watch server.js"[m
   },[m
   "keywords": [],[m
   "author": "",[m
   "license": "ISC",[m
   "type": "commonjs",[m
   "devDependencies": {[m
[31m-    "http-server": "^14.1.1"[m
[32m+[m[32m    "nodemon": "^3.0.1"[m
   }[m
 }[m
