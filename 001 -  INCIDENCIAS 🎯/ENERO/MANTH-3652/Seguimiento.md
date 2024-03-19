## Cambios que nos Piden

Tendría que aparecer un **nuevo apartado debajo de Tutor/es:** con la información de las valoraciones del seguimiento. Este nuevo apartado **solo aparecería si hay valoraciones.**

Tendría el siguiente **aspecto**:

**Valoración de las comisiones de seguimiento:  Curso académico  Convocatoria   Valoración 

Para cada valoración hay que mostrar el nombre del curso académico, el nombre de la convocatoria y la valoración.

Vamos a necesitar un nuevo método para obtener esta información, que se llamaría _**ObtenerDatosSeguimientoDoctorado**_


```java
 List<DatosSeguimientoDoctoradoVo> ObtenerDatosSeguimientoDoctorado (idExpedienteAcademico)
```

Vamos a necesitar una nueva clase _**DatosSeguimientoDoctoradoVo.java**_, con las propiedades: periodoAcademico, convocatoria y valoración  
Y la consulta (_**obtenerDatosSeguimientoDoctorado**_) para obtener los datos de las valoraciones, de haberlas sería:

```sql
	SELECT s.periodoacademico_id as periodoAcademico, nvl(cps.descripcion,'prueba') as convocatoria, s.valoracion as valoracion
	FROM dot_solicitudadmisiondoctorado sol
	INNER JOIN dot_solicitudplaninvestigacion sp on sp.solicitudadmisiondoctorado_id = sol.id
	INNER JOIN dot_seguimiento s on s.solicitud_id = sp.id
	LEFT JOIN convocatoriaplandocente_seguimiento cps on cps.id = s.convocatoria_id
	INNER JOIN alumno a ON a.id = sol.alumno_id
	INNER JOIN ofertaformativa ofe ON ofe.id = sol.ofertaformativasolicitada_id
	INNER JOIN expediente e ON e.persona_idtercero = a.persona_idtercero
	INNER JOIN expedienteespecifico ee ON ee.expedientegeneral_id = e.id
	INNER JOIN expedienteacademico ea ON ea.id = ee.id and ea.ofertaformativa_id = ofe.id
	WHERE sol.estadosolicitudadmisiondoctorado = 'ADMITIDA'
	AND ee.id = :idExpedienteAcademico
```

## Clases Modificadas

* En la clase  **JpaExpedienteAcademicoDao.java**   se ha creado estos dos métodos con uno obtenemos de base de datos los datos y el segundo es para transformar los datos en los tipos que nos piden
  ```java
public List<DatosSeguimientoDoctoradoVo> obtenerDatosSeguimientoDoctorado(Long idExpedienteAcademico) {

	final EntityManager em = JpaUtil.getEntityManager(this.getJpaTemplate().getEntityManagerFactory());
	final javax.persistence.Query query =
	em.createNamedQuery(JpaExpedienteAcademicoDao.OBTENER_DATOS_SEGUIMIENTO_DOCTORADO);
query.setParameter("idExpedienteAcademico", idExpedienteAcademico);

	final List<DatosSeguimientoDoctoradoVo> listaDatosSeguimiento = query.getResultList();
DatosInformePlanInvestigacionDoctoradoVO datosPlanInvestigacionDoctorado = new DatosInformePlanInvestigacionDoctoradoVO();

return transformarDatosSeguimiento(query.getResultList());
}
```

```java
	private List<DatosSeguimientoDoctoradoVo> transformarDatosSeguimiento(final List<Object[]> result) {

List<DatosSeguimientoDoctoradoVo> datosSeguimiento = new ArrayList<>();

final int CUSRO_ACADEMICO = 0;
final int CONVOCATORIA = 1;
final int VALORACION= 2;
List <String> seguimientoDoctorado = null;
for (Object[] fila : result) {
String cursoAcademico = DaoUtils.getString(fila[CUSRO_ACADEMICO]);
String convocatoria = DaoUtils.getString(fila[CONVOCATORIA]);
String valoracion = DaoUtils.getString(fila[VALORACION]);

DatosSeguimientoDoctoradoVo seguimientoVO = new DatosSeguimientoDoctoradoVo(cursoAcademico, convocatoria, valoracion);
datosSeguimiento.add(seguimientoVO);

}
return datosSeguimiento;
}
```

* Y meteremos esta consulta en el **orm.xml** 
```sql
	SELECT s.periodoacademico_id as periodoAcademico, nvl(cps.descripcion,'prueba') as convocatoria, s.valoracion as valoracion
	FROM dot_solicitudadmisiondoctorado sol
	INNER JOIN dot_solicitudplaninvestigacion sp on sp.solicitudadmisiondoctorado_id = sol.id
	INNER JOIN dot_seguimiento s on s.solicitud_id = sp.id
	LEFT JOIN convocatoriaplandocente_seguimiento cps on cps.id = s.convocatoria_id
	INNER JOIN alumno a ON a.id = sol.alumno_id
	INNER JOIN ofertaformativa ofe ON ofe.id = sol.ofertaformativasolicitada_id
	INNER JOIN expediente e ON e.persona_idtercero = a.persona_idtercero
	INNER JOIN expedienteespecifico ee ON ee.expedientegeneral_id = e.id
	INNER JOIN expedienteacademico ea ON ea.id = ee.id and ea.ofertaformativa_id = ofe.id
	WHERE sol.estadosolicitudadmisiondoctorado = 'ADMITIDA'
	AND ee.id = :idExpedienteAcademico
```

* Deberemos de crear en el vo lo correspondiente a la nueva lista que le vamos a pasar de datos, que en  vo 
**DatosInformePlanInvestigacionDoctoradoVO.java** metemos la lista.

```java

private List<String> seguimientoDoctorado = new ArrayList<>();

// get y set deseguimientoDoctorado
public String getSeguimientoDoctorado() {
	return this.getSeguimientoFromStringListSeguimientoDoctorado();
}

private String getSeguimientoFromStringListSeguimientoDoctorado() {
	if ((this.seguimientoDoctorado != null) && !this.seguimientoDoctorado.isEmpty()) {
	final StringBuilder sb = new StringBuilder();
	for (final String seguimiento : this.seguimientoDoctorado) {
	sb.append(seguimiento);
	}
	return sb.substring(0, sb.length() -1);
	}
	return SiesStringUtils.DEFAULT_RETURN;
}

public List<String> getSeguimiento() {
	return this.seguimientoDoctorado;
}

public void setSeguimientoDoctorado(List<DatosSeguimientoDoctoradoVo> listaDatosSeguimientoDoctorado) {
	for (DatosSeguimientoDoctoradoVo datosVo : listaDatosSeguimientoDoctorado) {
	seguimientoDoctorado.add(datosVo.toString());
}

}
```


* Después de crear estos métodos debemos de añadirlos para que lo muestre en el informe, en la clase **DefaultInformesExpService.java** dentro del método **generarPdfConSituacionAcademicaDoctorado** debemos de crear la lista que nos recupere la información 

```java
//llamada al nuevo metodo Seguimiento
List<DatosSeguimientoDoctoradoVo> listaDatosSeguimientoDoctorado = this.obtenerDatosSeguimientoDoctorado(vo.getExpedienteAcademico().getId());

//setear la nueva lista Seguimiento
datosPlanInvestigacion.setSeguimientoDoctorado(listaDatosSeguimientoDoctorado);
```

```java
private List<DatosSeguimientoDoctoradoVo> obtenerDatosSeguimientoDoctorado(
Long idExpedienteAcademico) throws InformesExpedientesException {
	try {
		return this.expedientesAcademicosDao
	.obtenerDatosSeguimientoDoctorado(idExpedienteAcademico);
	} catch (final Exception e) {
		final InformesExpedientesException retException = new InformesExpedientesException(
			"Error a recuperar la informacion de Seguimiento", e);
		throw retException;
	}
	}
```

Después de hacer esto solo quedaría la parte de cargar el valor de la etiqueta y de pintar los datos en el report
* En la clase **DefaultInformesExpService.java** debemos de crear un nuevo método la carga del nuevo texto 
dentro del método **cargarTextoPlanInvestigacionDoctoradoFromProperties** 

```java
pars.put("DOC_PLAN_INV_TEXT_SEGUIMIENTO_DOCTORADO", SiesStringUtils.getValueFromProperties(languageProperties,

	ConstantesCaProperties.DOC_PLAN_INVESTIGACION_SEGUIMIENTO_DOCTORADO));
```

 * Este será el delta para crearlo en base de datos **20231221_insert_configuration_DOC_NOMBRE_SEGUIMIENTO_DOCTORADO.sql**

* Luego a nivel de iReport **datosPlanInvestigacionDoctorado.jrxml** se debe crear un nuevo parameters y un fields .
 ![[Pasted image 20240102152716.png]]

* También debemos de crear un parámetro a nivel de ireport padre , que será el que englobe los subreport **situacionAcademicaDoctorado.jrxml
![[Pasted image 20240102152955.png]]