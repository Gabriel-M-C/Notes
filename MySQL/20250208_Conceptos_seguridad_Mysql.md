---
title: "Conceptos Seguridad en MySQL"
keywords:
  - Seguridad
  - MySQL
...

Para mantener un servidor MySQL seguro en cuanto a contraseñas, lo ideal es seguir estas **mejores prácticas**:

***

### **1️⃣ Usar Docker Secrets (Si usas Docker Swarm)**

Los **Docker Secrets** son la mejor opción porque no almacenan contraseñas en variables de entorno y solo son accesibles dentro del contenedor.

📌 **Ejemplo:**

* En lugar de usar `ENV MYSQL_ROOT_PASSWORD=tu_contraseña`, usa:
  ```yaml
  environment:
    MYSQL_ROOT_PASSWORD_FILE: /run/secrets/mysql_root_password
  secrets:
    - mysql_root_password
  ```
* Luego, crea el secreto con:
  ```sh
  echo "MiContraseñaSegura" | docker secret create mysql_root_password -
  ```

***

### **2️⃣ Usar un archivo `.env` (Si no usas Docker Swarm)**

Si no puedes usar **Docker Secrets**, usa un archivo `.env` para evitar exponer contraseñas en el `Dockerfile` o en `docker-compose.yml`.

📌 **Ejemplo de `.env`:**

```env
DB_PASSWORD=MiContraseñaSegura
```

Y en `docker-compose.yml` usa:

```yaml
environment:
  MYSQL_ROOT_PASSWORD: ${DB_PASSWORD}
```

⚠️ **Asegúrate de agregar `.env` a `.gitignore` para no subirlo a GitHub.**

***

### **3️⃣ No reutilizar contraseñas entre usuarios**

En tu Dockerfile tenías la misma contraseña para el usuario root y el usuario normal. No es recomendable.

📌 **Mejor práctica:** Usa credenciales separadas.

```yaml
environment:
  MYSQL_ROOT_PASSWORD_FILE: /run/secrets/mysql_root_password
  MYSQL_USER: my_user
  MYSQL_PASSWORD_FILE: /run/secrets/mysql_user_password
```

***

### **4️⃣ Usar contraseñas fuertes y cambiarlas periódicamente**

* Usa **mínimo 12 caracteres** con letras, números y símbolos.
* No uses contraseñas predeterminadas como `root` o `password123`.
* Cambia las contraseñas periódicamente y usa un gestor de contraseñas.

🔹 Puedes generar contraseñas seguras con:

```sh
openssl rand -base64 32
```

***

### **5️⃣ Deshabilitar acceso root remoto**

Por defecto, MySQL permite conexiones remotas para root, lo que es un riesgo.\
**Solución:** Edita `my.cnf` para restringir el acceso.

```ini
[mysqld]
bind-address = 127.0.0.1
```

Esto hace que MySQL solo acepte conexiones locales.

***

### **6️⃣ Usar Autenticación basada en Certificados**

Si el servidor está en producción, puedes usar **certificados SSL/TLS** para autenticar usuarios en lugar de contraseñas simples.

📌 **Ejemplo:**

```ini
[mysqld]
require_secure_transport=ON
ssl-ca=/etc/mysql/ca.pem
ssl-cert=/etc/mysql/server-cert.pem
ssl-key=/etc/mysql/server-key.pem
```

***

### **7️⃣ Usar una herramienta de gestión de secretos**

Si usas una infraestructura más avanzada, considera herramientas como:\
✅ **HashiCorp Vault**\
✅ **AWS Secrets Manager**\
✅ **Azure Key Vault**

***

### **8️⃣ Revisar Logs y Seguridad**

* Usa `fail2ban` o un firewall para evitar ataques de fuerza bruta.
* Revisa los logs de MySQL regularmente con:
  ```sh
  cat /var/log/mysql/error.log
  ```

***

### **🔒 Resumen de Mejores Prácticas 🔒**

✅ **Docker Secrets** para almacenar contraseñas (si usas Swarm).\
✅ **Archivos `.env`** en lugar de escribir contraseñas en `Dockerfile` o `docker-compose.yml`.\
✅ **Contraseñas diferentes** para root y usuarios normales.\
✅ **Contraseñas seguras** y cambios periódicos.\
✅ **Deshabilitar acceso remoto para root**.\
✅ **Habilitar SSL/TLS** para conexiones seguras.\
✅ **Monitorear logs** y usar herramientas de seguridad.

***

🔥 **¿Necesitas ayuda para aplicar esto en tu entorno Docker?** 🚀
