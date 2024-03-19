---
tags:
  - Java
  - SQL
  - RCP
link: 
Fecha Asignada: 2024-01-18T00:00:00
Despliegue:
---
---

## DESCRIPCION DE LO QUE NOS PIDEN

Necesitamos **implementar un nuevo método** para obtener la información de Base de Datos 

---

## PASOS A SEGUIR

Empezamos desde el RCP ,  lo primero que tenemos que mirar dentro de la clase que vamos a crear el nuevo método
es mirar que servicios vamos a meter el nuevo método.
Cuando tengamos claro que servicio vamos a utilizar  , miramos si ese servicio esta inyectado o necesitamos que nosotros lo inyectemos al rcp

Así encontraremos en el rcp un servicio
```java
private ExpedienteService expedientesService;
```

También debe tener en la misma clase un set
```java
public void setExpedientesService(ExpedienteService expedientesService) {
	this.expedientesService = expedientesService;
}
```

Después iremos **richclient-application-context.xml**  para mirar si esta inyectado el servicio
```xml
<bean id="masterAlumnosExpViewDescriptor" parent="abstractViewDescriptor"
p:form-class="es.uniovi.sies.exp.alumno.ui.form.AlumnoMasterForm">
	<property name="formProperties">
<util:map>
	<entry key="genericosService" value-ref="genericosService" />
	<entry key="expedientesAcademicosService" value-ref="expedienteAcademicoService" />
	<entry key="expedientesService" value-ref="expedientesService" />
	</util:map>
	</property>
</bean>
```
Sino estuviese inyectado lo deberíamos de añadir como los que se ven así