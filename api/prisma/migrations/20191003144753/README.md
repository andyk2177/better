# Migration `20191003144753`

This migration has been generated by Enoch at 10/3/2019, 2:47:53 PM.
You can check out the [state of the datamodel](./datamodel.prisma) after the migration.

## Database Steps

```sql
CREATE TABLE `campus`.`User` (
  `createdAt` datetime(3) NOT NULL DEFAULT '1970-01-01 00:00:00' ,
  `email` varchar(191) NOT NULL DEFAULT '' ,
  `id` varchar(191) NOT NULL  ,
  `name` varchar(191) NOT NULL DEFAULT '' ,
  `picture` varchar(191)   ,
  `role` varchar(191) NOT NULL DEFAULT 'STUDENT' ,
  `updatedAt` datetime(3) NOT NULL DEFAULT '1970-01-01 00:00:00' ,
  PRIMARY KEY (`id`)
)
DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

CREATE TABLE `campus`.`Course` (
  `catalog_number` int NOT NULL DEFAULT 0 ,
  `class_number` int NOT NULL DEFAULT 0 ,
  `component` varchar(191) NOT NULL DEFAULT '' ,
  `createdAt` datetime(3) NOT NULL DEFAULT '1970-01-01 00:00:00' ,
  `id` varchar(191) NOT NULL  ,
  `name` varchar(191) NOT NULL DEFAULT '' ,
  `slug` varchar(191)   ,
  `subject` varchar(191) NOT NULL DEFAULT '' ,
  `term` int NOT NULL DEFAULT 0 ,
  `title` varchar(191)   ,
  `updatedAt` datetime(3) NOT NULL DEFAULT '1970-01-01 00:00:00' ,
  PRIMARY KEY (`id`)
)
DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

CREATE TABLE `campus`.`CourseMessage` (
  `createdAt` datetime(3) NOT NULL DEFAULT '1970-01-01 00:00:00' ,
  `id` varchar(191) NOT NULL  ,
  `message` varchar(191) NOT NULL DEFAULT '' ,
  `updatedAt` datetime(3) NOT NULL DEFAULT '1970-01-01 00:00:00' ,
  PRIMARY KEY (`id`)
)
DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

CREATE TABLE `campus`.`_CourseToUser` (
  `A` varchar(191)  ,
 FOREIGN KEY (`A`) REFERENCES `campus`.`Course`(`id`) ON DELETE CASCADE,
  `B` varchar(191)  ,
 FOREIGN KEY (`B`) REFERENCES `campus`.`User`(`id`) ON DELETE CASCADE
)
DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

CREATE TABLE `campus`.`_CourseMessageToUser` (
  `A` varchar(191)  ,
 FOREIGN KEY (`A`) REFERENCES `campus`.`CourseMessage`(`id`) ON DELETE CASCADE,
  `B` varchar(191)  ,
 FOREIGN KEY (`B`) REFERENCES `campus`.`User`(`id`) ON DELETE CASCADE
)
DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

ALTER TABLE `campus`.`CourseMessage` ADD COLUMN `course` varchar(191)  ,
ADD FOREIGN KEY (`course`) REFERENCES `campus`.`Course`(`id`) ON DELETE SET NULL;

CREATE UNIQUE INDEX `User.email` ON `campus`.`User`(`email`)

CREATE UNIQUE INDEX `Course.slug` ON `campus`.`Course`(`slug`)

CREATE UNIQUE INDEX `Course.class_number` ON `campus`.`Course`(`class_number`)

CREATE UNIQUE INDEX `_CourseToUser_AB_unique` ON `campus`.`_CourseToUser`(`A`,`B`)

CREATE UNIQUE INDEX `_CourseMessageToUser_AB_unique` ON `campus`.`_CourseMessageToUser`(`A`,`B`)
```

## Changes

```diff
diff --git datamodel.mdl datamodel.mdl
migration ..20191003144753
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,56 @@
+datasource db {
+  provider = "mysql"
+  url      = env("MYSQL_URL")
+}
+
+generator photonjs {
+  provider = "photonjs"
+}
+
+generator nexus_prisma {
+  provider = "nexus-prisma"
+}
+
+model User {
+  id              String          @id @default(cuid())
+  name            String
+  email           String          @unique
+  picture         String?
+  courses         Course[]
+  course_messages CourseMessage[]
+  role            Role            @default(STUDENT)
+  createdAt       DateTime        @default(now())
+  updatedAt       DateTime        @updatedAt
+}
+
+model Course {
+  id             String          @id @default(cuid())
+  name           String
+  title          String?
+  term           Int
+  slug           String?         @unique
+  subject        String
+  catalog_number Int
+  component      String
+  class_number   Int             @unique
+  users          User[]
+  messages       CourseMessage[]
+  createdAt      DateTime        @default(now())
+  updatedAt      DateTime        @updatedAt
+}
+
+model CourseMessage {
+  id        String   @id @default(cuid())
+  course    Course
+  user      User[]
+  message   String
+  createdAt DateTime @default(now())
+  updatedAt DateTime @updatedAt
+}
+
+enum Role {
+  ADMIN
+  PROFESSOR
+  TEACHING_ASSISSTANT
+  STUDENT
+}
```

## Photon Usage

You can use a specific Photon built for this migration (20191003144753)
in your `before` or `after` migration script like this:

```ts
import Photon from '@generated/photon/20191003144753'

const photon = new Photon()

async function main() {
  const result = await photon.users()
  console.dir(result, { depth: null })
}

main()

```