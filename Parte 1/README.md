# Liga MX XML + DTD

## Propósito

Este proyecto representa en XML los resultados de los partidos de Liga MX correspondientes al domingo 27 de septiembre de 2026 y define un DTD externo para validar su estructura.

La guía de la práctica solicita modelar una liga, una jornada, partidos, equipos, marcador, estadio, estado y estadísticas. La estructura utilizada sigue esa jerarquía.

## Fuente de resultados

Los marcadores y datos básicos fueron contrastados con resultados publicados para la Jornada 10 del Apertura 2026. Para el domingo 27 de septiembre aparecen cuatro partidos: Pumas UNAM 2-3 Atlético San Luis, Tigres UANL 1-0 Puebla, Santos Laguna 1-0 Pachuca y Cruz Azul 3-3 Toluca. Los tres partidos restantes de la jornada aparecen el lunes 28 de septiembre y por ello no forman parte de este XML.

## Estructura

```text
liga
└── jornada
    ├── partido
    │   ├── equipoLocal
    │   ├── equipoVisitante
    │   ├── marcador
    │   │   ├── golesLocal
    │   │   └── golesVisitante
    │   ├── estadio
    │   ├── estado
    │   ├── goles
    │   │   └── gol*
    │   └── estadisticas
    │       └── estadistica*
    └── partido...
```

## Elementos y atributos

- `liga`: elemento raíz; `nombre` y `temporada` son atributos obligatorios.
- `jornada`: contiene uno o más partidos; `numero`, `fecha` y `competencia` son atributos obligatorios.
- `partido`: contiene exactamente un local, un visitante, un marcador, estadio, estado, goles y estadísticas. Su atributo `id` es de tipo `ID`, por lo que debe ser único dentro del documento.
- `equipoLocal` y `equipoVisitante`: elementos porque representan contenido textual propio del partido y su posición en la secuencia distingue la localía.
- `marcador`: agrupa los goles de ambos equipos.
- `gol`: elemento vacío con atributos `equipo`, `minuto` y `jugador`.
- `estadistica`: elemento textual con atributos `nombre` y `equipo`; se permite cero o más para no inventar información cuando la fuente no la expone.

## ¿Por qué usar ID?

El identificador de cada partido se declara como `ID` en el DTD. A diferencia de un `CDATA` común, XML exige que cada valor `ID` sea único dentro del documento. Esto permite identificar inequívocamente cada partido y evita duplicados accidentales.

## Estadísticas

La guía pide representar estadísticas como posesión, tiros, tiros a puerta, faltas, tarjetas y tiros de esquina. El modelo las admite mediante `estadistica`, pero en esta versión no se rellenan estadísticas numéricas que no pudieron verificarse en los resultados indexados consultados de ESPN para esos cuatro encuentros. Se evita así inventar datos.

Los goles y sus minutos se representan aparte porque están disponibles en las fuentes consultadas y forman parte de la información del partido.

## Asociación con el DTD

`xml/resultados.xml` contiene:

```xml
<!DOCTYPE liga SYSTEM "../dtd/resultados.dtd">
```

La ruta es relativa al archivo XML: desde `xml/` se sube un nivel (`..`) y se entra en `dtd/`.

## Validación

Con `xmllint` se puede validar:

```bash
xmllint --noout --valid xml/resultados.xml
```

Si el comando termina sin errores, el documento es válido respecto al DTD externo.

Para comprobar el archivo de prueba negativa:

```bash
xmllint --noout --valid xml/resultados-invalido.xml
```

Debe producir un error porque falta el elemento obligatorio `equipoVisitante`.

## Pruebas negativas de la guía

La guía propone probar:

1. Falta de equipo visitante.
2. Dos equipos locales.
3. Orden incorrecto de elementos.
4. Falta de atributo obligatorio.
5. ID duplicado.
6. Elemento no declarado.

El archivo `xml/resultados-invalido.xml` contiene la primera prueba. Las restantes pueden realizarse copiando un partido y modificando una sola regla por prueba.

Importante: un XML puede estar bien formado y aun así no ser válido frente a su DTD.

## Estructura del repositorio

```text
liga-mx-xml/
├── README.md
├── xml/
│   ├── resultados.xml
│   └── resultados-invalido.xml
└── dtd/
    └── resultados.dtd
```
