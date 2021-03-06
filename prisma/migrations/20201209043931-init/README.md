# Migration `20201209043931-init`

This migration has been generated by sesia at 12/9/2020, 1:39:31 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TABLE `User` (
    `id` INT NOT NULL AUTO_INCREMENT,
    `createdAt` DATETIME(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3),
    `updatedAt` DATETIME(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3),
    `name` VARCHAR(191) NOT NULL,
    `email` VARCHAR(191) NOT NULL,
    `phoneNumber` VARCHAR(191) NOT NULL,
    `avatar` VARCHAR(191) NOT NULL DEFAULT '',
UNIQUE INDEX `User.name_unique`(`name`),
UNIQUE INDEX `User.email_unique`(`email`),
UNIQUE INDEX `User.phoneNumber_unique`(`phoneNumber`),

    PRIMARY KEY (`id`)
) DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci

CREATE TABLE `Post` (
    `id` INT NOT NULL AUTO_INCREMENT,
    `title` VARCHAR(191) NOT NULL,
    `text` VARCHAR(191),
    `postCategory` ENUM('COMMON', 'JOB', 'ETC', 'REPAIR') NOT NULL,
    `postAuthorId` INT NOT NULL,

    PRIMARY KEY (`id`)
) DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci

CREATE TABLE `Comment` (
    `id` INT NOT NULL AUTO_INCREMENT,
    `text` VARCHAR(191) NOT NULL,
    `postId` INT NOT NULL,
    `commentAuthorId` INT NOT NULL,
    `createdAt` DATETIME(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3),
    `updatedAt` DATETIME(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3),

    PRIMARY KEY (`id`)
) DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci

CREATE TABLE `Like` (
    `id` INT NOT NULL AUTO_INCREMENT,
    `userId` INT,
    `postId` INT NOT NULL,

    PRIMARY KEY (`id`)
) DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci

CREATE TABLE `OverWork` (
    `id` INT NOT NULL AUTO_INCREMENT,
    `date` DATETIME(3),
    `yearMonth` VARCHAR(191),
    `yearMonthDat` VARCHAR(191),
    `week` INT NOT NULL,
    `location` VARCHAR(191) NOT NULL,
    `content` VARCHAR(191) NOT NULL,
    `overWorkCategory` ENUM('EARLY', 'NIGHT', 'ALLNIGHT', 'WEEKEND', 'OVERTIME') NOT NULL,
    `amount` INT NOT NULL,
    `authorId` INT NOT NULL,
    `createdAt` DATETIME(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3),
    `updatedAt` DATETIME(3) NOT NULL DEFAULT CURRENT_TIMESTAMP(3),

    PRIMARY KEY (`id`)
) DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci

CREATE TABLE `File` (
    `id` INT NOT NULL AUTO_INCREMENT,
    `url` VARCHAR(191) NOT NULL,
    `postId` INT,

    PRIMARY KEY (`id`)
) DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci

CREATE TABLE `_FollowRelation` (
    `A` INT NOT NULL,
    `B` INT NOT NULL,
UNIQUE INDEX `_FollowRelation_AB_unique`(`A`,
`B`),
INDEX `_FollowRelation_B_index`(`B`)
) DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci

ALTER TABLE `Post` ADD FOREIGN KEY (`postAuthorId`) REFERENCES `User`(`id`) ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE `Comment` ADD FOREIGN KEY (`postId`) REFERENCES `Post`(`id`) ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE `Comment` ADD FOREIGN KEY (`commentAuthorId`) REFERENCES `User`(`id`) ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE `Like` ADD FOREIGN KEY (`postId`) REFERENCES `Post`(`id`) ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE `Like` ADD FOREIGN KEY (`userId`) REFERENCES `User`(`id`) ON DELETE SET NULL ON UPDATE CASCADE

ALTER TABLE `OverWork` ADD FOREIGN KEY (`authorId`) REFERENCES `User`(`id`) ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE `File` ADD FOREIGN KEY (`postId`) REFERENCES `Post`(`id`) ON DELETE SET NULL ON UPDATE CASCADE

ALTER TABLE `_FollowRelation` ADD FOREIGN KEY (`A`) REFERENCES `User`(`id`) ON DELETE CASCADE ON UPDATE CASCADE

ALTER TABLE `_FollowRelation` ADD FOREIGN KEY (`B`) REFERENCES `User`(`id`) ON DELETE CASCADE ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20201209043931-init
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,93 @@
+generator client {
+  provider = "prisma-client-js"
+}
+
+datasource db {
+  provider = "mysql"
+  url = "***"
+}
+
+model User {
+  id          Int        @id @default(autoincrement())
+  createdAt   DateTime   @default(now())
+  updatedAt   DateTime   @default(now())
+  name        String     @unique
+  email       String     @unique
+  phoneNumber String     @unique
+  avatar      String     @default(value: "")
+  posts       Post[]
+  comments    Comment[]
+  likes       Like[]
+  overWorks   OverWork[]
+  following   User[]     @relation("FollowRelation", references: [id])
+  followers   User[]     @relation("FollowRelation", references: [id])
+}
+
+model Post {
+  id           Int          @id @default(autoincrement())
+  title        String
+  text         String?
+  postCategory PostCategory
+  comments     Comment[]
+  likes        Like[]
+  postAuthor   User         @relation(fields: [postAuthorId], references: [id])
+  postAuthorId Int
+  files        File[]
+}
+
+model Comment {
+  id              Int      @id @default(autoincrement())
+  text            String
+  post            Post     @relation(fields: [postId], references: [id])
+  postId          Int
+  commentAuthor   User     @relation(fields: [commentAuthorId], references: [id])
+  commentAuthorId Int
+  createdAt       DateTime @default(now())
+  updatedAt       DateTime @default(now())
+}
+
+model Like {
+  id     Int   @id @default(autoincrement())
+  post   Post  @relation(fields: [postId], references: [id])
+  User   User? @relation(fields: [userId], references: [id])
+  userId Int?
+  postId Int
+}
+
+model OverWork {
+  id               Int              @id @default(autoincrement())
+  date             DateTime?
+  yearMonth        String?
+  yearMonthDat     String?
+  week             Int
+  location         String
+  content          String
+  overWorkCategory OverWorkCategory
+  amount           Int
+  author           User             @relation(fields: [authorId], references: [id])
+  authorId         Int
+  createdAt        DateTime         @default(now())
+  updatedAt        DateTime         @default(now())
+}
+
+model File {
+  id     Int    @id @default(autoincrement())
+  url    String
+  post   Post?  @relation(fields: [postId], references: [id])
+  postId Int?
+}
+
+enum PostCategory {
+  COMMON
+  JOB
+  ETC
+  REPAIR
+}
+
+enum OverWorkCategory {
+  EARLY
+  NIGHT
+  ALLNIGHT
+  WEEKEND
+  OVERTIME
+}
```


