# django con Python
Tutorial de Django


### PASO 1: Instala Django desde cmd 
Pip es un sistema de gestión de paquetes utilizado para instalar y administrar paquetes de software en Python. La instalación de Django es directa, simplemente escribe lo siguiente:

      pip install django
      py -m venv project-name
      project-name\Scripts\activate.bat
      py -m pip install Django
      py -m pip install colorama
        


### PASO 2: Crear proyecto

Crea el proyecto Django utilizando la utilidad de administración `django-admin`:

      django-admin startproject ejemplo
        
   - **__init.py**: un archivo vacío para indicar a Python de que la carpeta es un paquete. 
   - **settings.py**: contiene la configuración del proyecto creado. 
   - **urls.py**: contiene la información para generar las URLs de nuestro proyecto.
   - **manage.py**: utilidad para interactuar con el proyecto.
   
   Inicia el servidor con el comando `python manage.py runserver` dentro del directorio del proyecto con la misma consola.

### PASO 3: Crear APP

Crea la aplicación ejecutando el siguiente comando:

`python manage.py startapp ejemploApp`

Dentro del directorio de la aplicación, crear un fichero llamado `urls.py` que utilizaremos para mapear las URLs de nuestra aplicación.

Incluir la app dentro del fichero `settings.py` del proyecto, añadiendo el nombre a la lista `INSTALLED_APPS`:
	
	 INSTALLED_APPS = [
	 	'ejemploApp',
	 	# ...
	 ]

### PASO 4: Crear y configurar rutas y vistas

Dentro del directorio de la app, abrir `views.py` y añadir lo siguiente:

```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Listado de estudiantes")
```
    
Abrir (o crear) el fichero `urls.py` y añadir el patrón para la siguiente ruta:
   ```python
from django.urls import path
from . import views
   
urlpatterns = [
    path('', views.index, name='index'),
]
   ```
Dentro del directorio del proyecto, editar `urls.py` para incluir la redirección al fichero `urls.py` de la aplicación. El resultado debe ser el siguiente (no olvides importar la librería `include` de `django.urls`):
   ```python
from django.contrib import admin
from django.urls import include, path
  
urlpatterns = [
    path('ejemploApp/', include('ejemploApp.urls')),
    path('admin/', admin.site.urls),
]
   ```

De este modo tenemos un `urls.py` en cada directorio de nuestras aplicaciones gestionados desde el `urls.py` del directorio del proyecto.

### PASO 5: Plantillas
Para que nuestra aplicación muestre vistas HTML en lugar de una cadena de texto, utilizaremos plantillas (templates). Crea tu primera plantilla, `student_list.html`, dentro de una carpeta llamada `templates`. El contenido será el siguiente:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Django University App</title>
</head>
<body>
<h1>Hola!</h1>
</body>
</html>
```

Las plantillas podrán recibir un diccionario de datos para mostrar aquella información que nos interese. Vamos a probar a pasar una variable que incluya el nombre de la clase:

```python
def index(request):
    context = {'clase': 'Aprendiendo Django'}
    return render(request, 'student_list.html', context)
```

Para mostrarla en la vista simplemente tendremos que escribir el nombre de la variable entre los símbolos `{{ }}`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ejemploApp</title>
</head>
<body>
<h1>Hello!</h1>
<h2>Clase: {{ clase }}</h2>
</body>
</html>
```

### PASO 6: Crear modelo de la DB
Edita el fichero `models.py` de la aplicación creando las clase Student:

   ```python
from django.db import models
    
class Student(models.Model):
	# No es necesario crear un campo para la Primary Key, Django creará automáticamente un IntegerField.
	nombre = models.CharField(max_length=50)
  	apellidos = models.CharField(max_length=50)
	edad = models.IntegerField()
  	email = models.EmailField()
```    
Aplica los cambios realizados en el modelo mediante los siguientes comandos:
    
   ```
python manage.py makemigrations ejemploApp
python manage.py migrate   
   ```

### PASO 7: Añade y consulta registros desde la API de Django

Accede al intérprete interactivo de Django escribiendo el siguiente comando en la misma CMD del comienzo:

	python manage.py shell

Una vez dentro, ya puedes comenzar a crear y consultar registros.
   ```python
	>>> from ejemploApp.models import Student
	>>> estudiante1 = Student(nombre="Julio", apellidos="Ammorrortu Jainaga", edad=22, email="julio@mail.com")
	>>> estudiante1.save()
	# Django le asigna un id.
	>>> estudiante1.id
	1
	
	# Acceder a sus atributos
	>>> estudiante1.nombre
	'Julio'

    #Salir
	>>>> quit()
   ```


### PASO 8: Actualizar la vista para que muestre la información de la base de datos

Modificamos la vista que teníamos definida en `views.py` para que recoja todos los registros desde la base de datos y se los pase a la vista.

```python
from django.shortcuts import render
from .models import Student

def index(request):
    estudiantes = Student.objects.all()
    context = {'clase': 'Aprendiendo Django', 'estudiantes': estudiantes}
    return render(request, 'student_list.html', context)
```

Ahora que ya tenemos la información disponible en la plantilla, editamos la plantilla para que itere por cada estudiante y lo muestre por pantalla:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ejemploApp</title>
</head>
<body>
<h1>Hello!</h1>
<h2>Clase: {{ clase }}</h2>
{% if estudiantes %}
<ul>
    {% for estudiante in estudiantes %}
    <li>{{ estudiante.nombre }}</li>
    {% endfor %}
</ul>
{% endif %}
</body>
</html>
```

Recarga la pestaña y LISTO!
