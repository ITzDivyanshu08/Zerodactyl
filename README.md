# ⭐ Pterodactyl Panel - Dockerized ⭐

Effortlessly deploy Pterodactyl Panel in a fully containerized Docker environment with a structured and optimized setup.

(Yes, PHP-FPM and Cron share a container, but Cron is lightweight, so it’s fine, right?)

This setup might be compatible with Docker Swarm, but it hasn’t been fully tested yet.


---

# 🚀 Quick Setup Guide

1️⃣ Download the Configuration

📥 Copy the docker-compose.yml file to your local system.

2️⃣ Configure Database Credentials

🛠 Open docker-compose.yml and set a strong username and password for your database.

Ensure that:

MariaDB → MYSQL_USER / MYSQL_PASSWORD

PHP-FPM → DB_USERNAME / DB_PASSWORD
✅ Credentials must match between them!


3️⃣ Start the Containers

🚀 Run the following command to bring up the stack:

docker-compose up -d

4️⃣ Find Your PHP-FPM Container Name

🔍 Run the following command:

docker ps

Look for a container with a name like pterodactyl_php-fpm_1.

5️⃣ Create an Admin User

👤 Execute the following command inside your PHP-FPM container:

docker exec -it pterodactyl_php-fpm_1 php artisan p:user:make

Follow the prompts to configure your first administrator account for the panel.


---

# ⚡ Using an External MySQL/MariaDB Server

If you want to connect to an external MySQL/MariaDB database instead of using the built-in MariaDB container, follow these steps:

1️⃣ Remove the Database Service

❌ Delete the database service section from docker-compose.yml.

2️⃣ Update Dependencies

🔄 In the PHP-FPM service section, remove database from the depends_on list.

3️⃣ Modify Database Connection Settings

🔧 Change the DB_HOST and DB_PORT values in the PHP-FPM environment variables to match your external database server.


---

# ✅ Done! Now your Pterodactyl Panel is fully functional in a Dockerized environment! 🚀

