# Using htmx with Bryntum Scheduler: Backend

Install the dependencies by running the following command:

```bash
npm install
```

Run the server locally using the following command:

```bash
npm run dev
```

In the `utils/dbConnect.js` file, the Express server uses the MySQL2 library to connect to the MySQL database. A connection pool is created using the MySQL2 `createPool` method. The connection pool is exported into the `server.js` file where it's used to run database queries for CRUD operations using the `query` method.

Now create a `.env` file in the root folder and add the following lines for connecting to the MySQL database that we’ll create:

```
HOST=localhost
PORT=1338
MYSQL_USER=root
PASSWORD=
DATABASE=bryntum_scheduler
FRONTEND_URL=http://localhost:5173
```

Don’t forget to add the root password for your MySQL server.

## Set up a MySQL database locally

We’ll install MySQL Server and MySQL Workbench. MySQL Workbench is a MySQL GUI that we’ll use to create a database with tables for the Bryntum Scheduler data and to run queries. Download MySQL Server and MySQL Workbench from the MySQL community downloads page. If you’re using Windows, you can use the MySQL Installer to download the MySQL products. 

Use the default configurations when configuring MySQL Server and Workbench. Make sure that you configure the MySQL Server to start at system startup for your convenience.

Open the MySQL Workbench desktop application. Open the local instance of the MySQL Server that you configured.

We’ll write our MySQL queries in the query tab and execute the queries by pressing the yellow lightning bolt button.

### Create a MySQL database for the Bryntum Scheduler data: Adding tables and example data

Let’s run some MySQL queries in MySQL Workbench to create, use, and populate a database for our Bryntum Scheduler data. Execute the following query to create a database called `bryntum_scheduler`:


```sql
CREATE DATABASE bryntum_scheduler;
```

Run the following query so that we set our newly created database for use:

```sql
USE bryntum_scheduler;
```

Create a table for the resources data:

```sql
create TABLE resources
(
    `id`         int          NOT NULL AUTO_INCREMENT,
    `name`       varchar(255) NOT NULL,
    `eventColor` varchar(255) DEFAULT NULL,
    `readOnly`   boolean      DEFAULT FALSE,
    PRIMARY KEY (`id`)
) ENGINE = INNODB
  AUTO_INCREMENT = 1;
```

Create a table for the events data:

```sql
create TABLE events
(
    `id`             int          NOT NULL AUTO_INCREMENT,
    `name`           varchar(255) NOT NULL,
    `readOnly`       boolean               DEFAULT FALSE,
    `resourceId`     int                   DEFAULT NULL,
    `timeZone`       varchar(255)          DEFAULT NULL,
    `draggable`      boolean               DEFAULT TRUE,
    `resizable`      varchar(255)          DEFAULT null,
    `children`       varchar(255)          DEFAULT null,
    `allDay`         boolean               DEFAULT FALSE,
    `duration`       float(11, 2) unsigned DEFAULT NULL,
    `durationUnit`   varchar(255)          DEFAULT 'day',
    `startDate`      datetime              DEFAULT NULL,
    `endDate`        datetime              DEFAULT NULL,
    `exceptionDates` json                  DEFAULT null,
    `recurrenceRule` varchar(255)          DEFAULT null,
    `cls`            varchar(255)          DEFAULT null,
    `eventColor`     varchar(255)          DEFAULT null,
    `eventStyle`     varchar(255)          DEFAULT null,
    `iconCls`        varchar(255)          DEFAULT null,
    `style`          varchar(255)          DEFAULT null,
    CONSTRAINT `fk_events_resourceId` FOREIGN KEY (`resourceId`) REFERENCES `resources` (`id`) ON DELETE CASCADE,
    INDEX (`resourceId`),
    PRIMARY KEY (`id`)
) ENGINE = INNODB
  AUTO_INCREMENT = 1;
```

The `exceptionDates` field has a type of `json`. It's a field that may be an object. MySQL does not have an object field type so we’ll use the `json` type instead. The data for these fields will need to be stringified before it's inserted into the database and parsed when it's retrieved from the database.

Now add some example resources data to the resources table:

```sql
INSERT INTO resources (`name`, `eventColor`, `readOnly`) 
VALUES 
    ('Dillon', 'purple', FALSE),
    ('Peter', 'violet', FALSE),
    ('Kate', 'blue', FALSE);
```

Add some example events data to the events table:

```sql
INSERT INTO events (
    `name`, 
    `readOnly`, 
    `resourceId`, 
    `timeZone`, 
    `draggable`, 
    `resizable`, 
    `allDay`, 
    `startDate`, 
    `endDate`, 
    `recurrenceRule`, 
    `cls`, 
    `eventColor`
) 
VALUES 
    ('Intern training', FALSE, 1, NULL, TRUE, NULL, FALSE, '2023-11-29T13:00', '2023-11-29T17:00', NULL, NULL, NULL),
    ('Product launch webinar', FALSE, 2, NULL, TRUE, NULL, FALSE, '2023-11-29T14:10', '2023-11-29T16:00', NULL, NULL, NULL),
    ('Tech support meeting', FALSE, 3, NULL, TRUE, NULL, FALSE, '2023-11-29T16:10', '2023-11-29T18:00', NULL, NULL, NULL),
    ('Management meeting', FALSE, 3, NULL, TRUE, NULL, FALSE, '2023-11-29T09:00', '2023-11-29T11:30', NULL, NULL, NULL);
```

You’ll be able to view the example resources data by running the following query:

```sql
SELECT * FROM resources;
```

You’ll be able to view the example events data by running the following query:

```sql
SELECT * FROM events;
```

### Create a MySQL table for extra information about the resources

Create a table to store extra information about the resources:

```sql
CREATE TABLE resource_details (
    resourceId INT PRIMARY KEY,
    jobTitle VARCHAR(255),
    yearsExperience INT,
    yearsWithCompany INT,
    age INT,
    gender VARCHAR(50),
    additionalInfo TEXT,
    FOREIGN KEY (resourceId) REFERENCES resources(id) 
);
```

Add some example resource details data to the table:

```sql
INSERT INTO resource_details (resourceId, jobTitle, yearsExperience, yearsWithCompany, age, gender, additionalInfo) VALUES (1, 'Software Engineer', 5, 3, 30, 'Male', 'Expert in Java and Python'), (2, 'Project Manager', 8, 5, 35, 'Male', 'Certified PMP, skilled in Agile methodologies'), (3, 'UX Designer', 4, 2, 28, 'Female', 'Specializes in user interface design and user experience');
```

With that, you have the backend setup for the Bryntum Scheduler.
