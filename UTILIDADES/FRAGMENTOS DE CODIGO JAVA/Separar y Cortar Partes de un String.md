---
tags:
  - Java
  - Split
  - SubString
link: 
Fecha Asignada: 2024-01-15T00:00:00
Despliegue:
---


> [!faq]- Que Nos Piden
> Partimos de un texto que obtenemos de base de datos como el siguiente :
> **Evaluación de seguimiento ordinaria 2022-2023** 
>necesitamos recortar este texto para solo quedarnos con la parte de texto, para ello  debemos  de hacer esto en el toString()  

#Java #Split #SubString

```java
public String toString() {

String cursoAcademicoFinal = cursoAcademico.substring(0, 4); //pasamos de 2023-2024 a 2023
String convocatoriaFinal = StringUtils.EMPTY;
if (!convocatoria.isEmpty()) {

String[] parts = convocatoria.split(" "); //cortamos en array el texto
// [Evaluación] [de] [seguimiento] [ordinario] [2023-2023]
int convocatoriaPartes = parts.length - 1;  // nos quedamos con las 4 primeras partes 
String periodo = parts[convocatoriaPartes];
int posicionPeriodo = convocatoria.indexOf(periodo);
convocatoriaFinal = convocatoria.substring(0, posicionPeriodo - 1);
}

String convocatoriaAncho = String.format("%-50s", convocatoriaFinal).replace(' ', ' ');
return cursoAcademicoFinal + " " + convocatoriaAncho + " " + valoracion + "\n";

}
