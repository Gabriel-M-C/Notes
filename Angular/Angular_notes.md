# Apuntes Angular

## Conceptos basicos Nodejs
nvm : node version manager (V.20.11)
npm : node package manager, gestor de paquetes
Atención: node_modules se borra si queremos compartir, regenerar con : npm install 

## Instalaciṕn Angular:
De manera global para el sistema.
npm install -g @angular/cli

Explorar npx para ejecutar si instalación ¿?

## Comandos

### Verificar version de angular y npm:
ng version && npm --version

### Crear proyecto angular:
```
ng new primeraApp
```

### Crear una interfaz :
```
ng g i interfaces/iproduct --type=interface
```

### Crear un servicio
```
ng g s services/nombreServicio --skip-tests 
```

### Levantar servidor :
```
ng s
``` 



### Crear un componente de "página"
```
ng g c pages/productList --skip-tests
```

### Crear un componente elemento de "página", estructura carpetas
```
ng g c pages/productList/productCard --skip-tests
```

### Borrar carpeta node_modules  :
```
rm -rf node_modules
```

### Regenerar carpeta node_modules  :
```
npm install
```

## Instalar librerias "dependencias" (-g para global)
En concreto bootstrap se instala solo en local...
```
npm install bootstrap
```
Recordar añadir la linea de estilos y javascript bootstrap  en ```angular.json``` 
```
"styles": [
    "src/styles.css",
    "node_modules/bootstrap/dist/css/bootstrap.min.css"
          ],
"scripts": [
    "node_modules/bootstrap/dist/js/bootstrap.bundle.min.js"
            ]
```