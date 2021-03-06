# Migration `20210104073352-init`

This migration has been generated by sesia at 1/4/2021, 4:33:52 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
ALTER TABLE `craneData` DROP FOREIGN KEY `craneData_ibfk_1`

ALTER TABLE `craneData` DROP FOREIGN KEY `craneData_ibfk_2`

ALTER TABLE `Post` MODIFY `postCategory` ENUM('JOB', 'REPAIR', 'ETC') NOT NULL

CREATE TABLE `CraneData` (
    `id` INT NOT NULL AUTO_INCREMENT,
    `createdAt` DATETIME(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3),
    `riggingIndex` INT NOT NULL,
    `craneName` VARCHAR(191),
    `craneCode` VARCHAR(191),
    `craneModeName` VARCHAR(191),
    `excelSheetName` VARCHAR(191),
    `riggingDataId` INT NOT NULL,
    `userId` INT,

    PRIMARY KEY (`id`)
) DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci

DROP TABLE `craneData`

ALTER TABLE `CraneData` ADD FOREIGN KEY (`riggingDataId`) REFERENCES `RiggingData`(`id`) ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE `CraneData` ADD FOREIGN KEY (`userId`) REFERENCES `User`(`id`) ON DELETE SET NULL ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration 20210104072525-init..20210104073352-init
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
   id              Int               @id @default(autoincrement())
@@ -18,9 +18,9 @@
   avatar          String            @default(value: "")
   posts           Post[]
   comments        Comment[]
   likes           Like[]
-  riggingRecords  craneData[]
+  riggingRecords  CraneData[]
   overWorks       OverWork[]
   following       User[]            @relation("FollowRelation", references: [id])
   followers       User[]            @relation("FollowRelation", references: [id])
 }
@@ -59,9 +59,9 @@
   userId    Int?
   postId    Int
 }
-model craneData {
+model CraneData {
   id             Int         @id @default(autoincrement())
   createdAt      DateTime    @default(now())
   riggingIndex   Int
   craneName      String?
```


