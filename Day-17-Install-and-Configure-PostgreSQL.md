# Day 17: Install and Configure PostgreSQL

## Challenge Description
The Nautilus application development team is deploying a new application that uses a PostgreSQL database. We need to set up the database server (`stdb01`) with the following requirements:

1.  Create a database user `kodekloud_roy` with the password `8FmzjvFU6S`.
2.  Create a database named `kodekloud_db3`.
3.  Grant full permissions to user `kodekloud_roy` on the `kodekloud_db3` database.
4.  **Constraint:** Do not restart the PostgreSQL server service.

## Key Concepts
*   **PostgreSQL User Management:** Creating users (roles) with specific passwords.
*   **Database Management:** Creating new logical databases within the PostgreSQL cluster.
*   **Privilege Management:** Granting access rights (`ALL PRIVILEGES`) to specific users for specific databases.

## Solution

We performed the following steps on the Database Server (`stdb01`).

### 1. Access PostgreSQL
First, we logged into the database server and switched to the system `postgres` user to access the administration console.

```bash
# Connect to stdb01
ssh peter@stdb01

# Switch to the postgres system user
sudo su - postgres

# Enter the PostgreSQL interactive terminal
psql
```

### 2. Create User and Database
Inside the `psql` shell, we executed the following SQL commands:

```sql
-- 1. Create the user with the specified password
CREATE USER kodekloud_roy WITH PASSWORD '8FmzjvFU6S';

-- 2. Create the database
CREATE DATABASE kodekloud_db3;

-- 3. Grant full permissions to the user on the new database
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db3 TO kodekloud_roy;
```

### 3. Exit
We exited the PostgreSQL shell.

```sql
\q
```

## Verification
We verified the setup by attempting to log in as the new user to the new database.

```bash
# While still on stdb01 (or as postgres user)
psql -U kodekloud_roy -d kodekloud_db3 -h localhost

# When prompted, we entered the password: 8FmzjvFU6S
```
The successful login confirmed that the user exists, the database exists, and the user has access to it.
