# Migration `20201215024957-init`

This migration has been generated by sesia at 12/15/2020, 11:49:57 AM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
ALTER TABLE `Like` ADD COLUMN     `createdAt` DATETIME(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3),
    ADD COLUMN     `updatedAt` DATETIME(3) NOT NULL

ALTER TABLE `Post` MODIFY `postCategory` ENUM('JOB', 'REPAIR', 'ETC') NOT NULL
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration 20201210114204-init..20201215024957-init
--- datamodel.dml
+++ datamodel.dml
@@ -3,9 +3,9 @@
 }
 datasource db {
   provider = "mysql"
-  url = "***"
+  url = "***"
 }
 model User {
   id              Int             @id @default(autoincrement())
@@ -50,13 +50,15 @@
   updatedAt       DateTime @default(now())
 }
 model Like {
-  id     Int   @id @default(autoincrement())
-  post   Post  @relation(fields: [postId], references: [id])
-  User   User? @relation(fields: [userId], references: [id])
-  userId Int?
-  postId Int
+  id        Int      @id @default(autoincrement())
+  createdAt DateTime @default(now())
+  updatedAt DateTime @updatedAt
+  post      Post     @relation(fields: [postId], references: [id])
+  User      User?    @relation(fields: [userId], references: [id])
+  userId    Int?
+  postId    Int
 }
 model RiggingRecord {
   id             Int      @id @default(autoincrement())
```


