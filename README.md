# Prueba Práctica

Ha recibido acceso a este repositorio para completar la prueba práctica para la plaza de Desarrollador Senior Java para la plataforma MAPS.

En este documento encontrará las instrucciones del problema que debe ser resuelto antes del día **miércoles 26 de septiembre**.

La solución de este problema no requiere la implementación de código fuente, sin embargo, una prueba de concepto en el lenguaje de su elección podría mejorar sus posibilidades de selección.

Para enviar su respuesta deberá clonar este respositorio en un repositorio público de su propiedad y agregar el archivo **SOLUCION.md** donde explique el algoritmo implementado de preferencia **en inglés**. Puede agregar todos los archivos adicionales que considere conveniente si implementa una prueba de concepto.

Una vez tenga una solución al problema, actualice su repositorio y envíe la URL al correo de donde recibió este mensaje. ***Se evaluará el último commit enviado antes de la mediaa noche del día miércoles 26.*** No se considerarán para la evaluación commits posteriores a esa hora.

## Aspectos a evaluar

* La solución propuesta debe de responder a la funcionalidad mínima esperada requerida en este documento.
* Se evaluará la eficiencia de la solución propuesta como su utilización de memoria.
* Documentación clara y consisa del funcionamiento de la solución propuesta.
* Escritura técnica en inglés.

## Descripción del Problema

Se desea generar una implementación de MAPS que permita llevar el control de asistencia y evaluaciones a un programa educativo de Gobierno. En total se requiere almacenar las notas obtenidas por los estudiantes a 8 jornadas de capacitación para las cuales se almacena la evaluación de una actividad y un exámen.

Adicionalmente se registra la asistencia del participante cada vez que asiste a una capacitación.

Una boleta de progreso en el programa al finalizar el mismo contendría los siguientes datos:

|Capacitación         |Evaluación|Exámen|Nota|
|---------------------|----------|------|----|
| Salud               |8.0       |8.0   |8.0 |
| Nutrición           |8.0       |8.0   |8.0 |
| Arte                |8.0       |8.0   |8.0 |
| Educación           |8.0       |8.0   |8.0 |
| Autoestima          |8.0       |8.0   |8.0 |
| Empleabilidad       |8.0       |8.0   |8.0 |
| Servicio al Cliente |8.0       |8.0   |8.0 |
| Mercadeo            |8.0       |8.0   |8.0 |
| Asistencia          | 30 de 40 |      |7.5 |
| Promedio final      |          |      |7.9 |

Las notas de cada participante se calculan de la siguiente manera:
```
Nota de capacitación = (Evaluación+Exámen)/2
Nota asistencia      = (Asistencia/40)*10
Primedio Final       = (SUMA(Notas capacitaciones ...)+Asistencia)/9
```

Al presentar su SmartCard el docente debe poder obtener la información de las notas asignadas para cada actividad y exámen por capacitación como el número total de asistencias del participante.

### Almacenamiento de Datos en SmartCard

MAPS utiliza SmartCards para realizar el almacenamiento de la información asociada a un "evento". Se considera un evento a la interacción entre el dispositivo móvil y la SmartCard para almacenar información en la misma.

Para lograr este propósito la SmartCard posee un archivo de almacenamiento no-volatil, con características de una lista circular con espacio máximo para 40 eventos.

Cada evento puede almacenar hasta un máximo 5 datos de forma independiente. Por ejemplo:

| Evento   | Datos (JSON) |
|----------|--------------|
|1         |{'asistencia': 1,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0}|

Al ser un registro cíclico al superar el tamaño de la lista (40 eventos) se sobreescribe el evento más antiguo.

Por ejemplo (Antes de escribir un nuevo evento):

| Evento   | Datos (JSON) |
|----------|--------------|
|1         |{'asistencia': 1,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0}    |
|2         |{'asistencia': 2,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0}|
|...       |...                                                                    |
|40        |{'asistencia': 40,'salud_examen': 8.0, 'salud_actividad': 8.0}         |

 (Después de escribir un nuevo evento):

| Evento   | Datos (JSON) |
|----------|--------------|
|1         |{'asistencia': 2,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0}       |
|...       |...                                                                           |
|39        |{'asistencia': 40,'salud_examen': 8.0, 'salud_actividad': 8.0}                |
|40        |{'asistencia': 41,'empleabilidad_examen': 8.0, 'empleabilidad_actividad': 8.0}|

Note que el índice menor de la lista corresponde al evento mas antiguo y el evento más viejo es borrado.

### Restricciones de acceso a la lista circular:

 1. Solo pueden agregarse elementos al final de la lista.
 2. Solo puede escribirse un único evento y sus datos asociados por vez. Es decir, si el docente desea registrar una asistencia y una nota, deberá primero guardar al asistencia y luego la nota asociada.
 3. No se pueden editar elementos existentes de la lista.
 4. La lista puede ser recorrida en cualquier dirección.

### Restricciones de ingreso de datos

1. El docente solo puede registrar un dato nuevo a la vez: Nota, asistencia o examen. 
2. El docente podría requerir "corregir" una nota sin modificar un dato ya ingresado en lista.
3. El docente no puede ingresar manualmente el número de asistencias. Estas son auto-incrementadas cada vez que se guarda un evento de asistencia.

# Funcionalidad Mínima requerida

Debe de diseñar un algoritmo (En pseudo-código o el lenguaje de su elección) que sea capaz de:

 1. Agregar información de asistencia a la lista de forma auto-incremental.
 2. Agregar las notas de exámenes y actividades de las capacitaciones con valores ingresados por el docente.
 3. En caso de que el docente ingrese una "correccion" de nota, deberá de almacenar la nota más reciente.
 4. Permita extraer de la lista circular todas las notas de exámenes, actividades y asistencia de forma individual.
 5. No deberá de almacenar más de 5 datos por "evento".

## Otros aspectos a tomarse en consideración en la evaluación

 1. Costo en tiempo del algoritmo.
 2. Número de entradas utilizadas de la lista para almacenar todas las variables.
 3. Nivel de dispersión de datos en la lista.
 4. Integridad de los datos almacenados. (Que el algoritmo no devuelva datos inconsistentes)

## Ejemplo de datos de entrada y salida:

Datos de entrada:
```
eventos = [
	{'asistencia': 1,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0},
	{'asistencia': 2,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0}
];
```
Funciones y salidas esperadas:
```
// Incrementa la asistencia
eventos = agregar_asistencia(eventos)
eventos = [
	{'asistencia': 1,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0},
	{'asistencia': 2,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0},
	{'asistencia': 3,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0}
];

// Incrementa la asistencia
eventos = agregar_asistencia(eventos)
eventos = [
	{'asistencia': 1,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0},
	{'asistencia': 2,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0},
	{'asistencia': 3,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0},
	{'asistencia': 4,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0}
];

// Agrega una nota
eventos = agregar_nota(eventos, {'salud_examen': 8.0})
eventos = [
	{'asistencia': 1,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0},
	{'asistencia': 2,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0},
	{'asistencia': 3,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0},
	{'asistencia': 4,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0},
	{'asistencia': 4,'salud_examen': 8.0}
];

// Agrega una nota
eventos = agregar_nota(eventos, {'salud_actividad': 8.0})
eventos = [
	{'asistencia': 1,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0},
	{'asistencia': 2,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0},
	{'asistencia': 3,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0},
	{'asistencia': 4,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0},
	{'asistencia': 4,'salud_examen': 8.0},
	{'asistencia': 4,'salud_examen': 8.0, 'salud_actividad': 8.0}
];

// Corrije una nota
eventos = agregar_nota(eventos, {'salud_actividad': 7.0})
eventos = [
	{'asistencia': 1,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0},
	{'asistencia': 2,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0},
	{'asistencia': 3,'mercadeo_examen': 8.0, 'mercadeo_actividad': 8.0},
	{'asistencia': 4,'autoestima_examen': 8.0, 'autoestima_actividad': 8.0},
	{'asistencia': 4,'salud_examen': 8.0},
	{'asistencia': 4,'salud_examen': 8.0, 'salud_actividad': 8.0},
	{'asistencia': 4,'salud_examen': 8.0, 'salud_actividad': 7.0}
];

// Ejemplo de procesamiento de salida
salida = procesar_lista(eventos);
JSON.stringify(salida)
{
	'asistencia': { 'asistencia': 4, 'total': 40, 'nota_asistencia': 0.1 },
	'mercadeo': { 'examen': 8.0, 'actividad': 8.0, 'nota': 8.0 },
	'autoestima': { 'examen': 8.0, 'actividad': 8.0, 'nota': 8.0 },
	'salud': { 'examen': 8.0, 'actividad': 7.0, 'nota': 7.5 },
	'nota_final': 2.6
}
```
**Nota:** Para el ejemplo se ha utilizado un límite de 3 datos máximo por evento. En su algoritmo puede utilizar hasta 5 datos por evento.
