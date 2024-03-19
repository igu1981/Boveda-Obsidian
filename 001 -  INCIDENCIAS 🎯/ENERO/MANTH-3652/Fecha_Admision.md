

## Cambios que nos Piden

Tendría que aparecer:

_Curso académico de ingreso: <nombre_cur_acad_ingreso>_

Para obtener esta información se puede utilizar el método _**obtenerDatosFechaAdmisionDoctorado**_ pero haciendo cambios, como:

a) Habría que modificar la clase _DatosInformeFechaAdmisionDoctoradoVO.java_ para añadir una nueva propiedad String periodoAcadAdmision

b) Habría que modificar la consulta del orm de exp-service "_obtenerDatosFechaAdmisionProgramaDoctorado_" y el resultado "_ResultadoObtenerDatosFechaAdmisionProgramaDoctorado_", para obtener también el periodo académico
```sql
SELECT sol.fechaadmision AS fechaAdmisionProgramaDoctorado,p.nombre AS periodoAcadAdmision
FROM dot_solicitudadmisiondoctorado sol
INNER JOIN alumno a ON a.id = sol.alumno_id
INNER JOIN ofertaformativa ofe ON ofe.id = sol.ofertaformativasolicitada_id
INNER JOIN expediente e ON e.persona_idtercero = a.persona_idtercero
INNER JOIN expedienteespecifico ee ON ee.expedientegeneral_id = e.id
INNER JOIN expedienteacademico ea ON ea.id = ee.id and ea.ofertaformativa_id = ofe.id
INNER JOIN periodoacademico p on p.id = sol.periodoacademico_id
WHERE sol.estadosolicitudadmisiondoctorado = 'ADMITIDA'
AND ee.id = :idExpedienteAcademico
```

## Clases Modificadas

* En la clase _DatosInformeFechaAdmisionDoctoradoVO.java_  hemos añadido  una nueva propiedad String **periodoAcadAdmision**
```java
private String periodoAcadAdmision;
/**
*
* @return
*/
public String getPeriodoAcadAdmision() {
	return periodoAcadAdmision;
}

/**
*
* @param periodoAcadAdmision
*/
public void setPeriodoAcadAdmision(String periodoAcadAdmision) {
	this.periodoAcadAdmision = periodoAcadAdmision;
}
```

* Hemos  modificado la consulta del orm de exp-service "_obtenerDatosFechaAdmisionProgramaDoctorado_" y el resultado "_ResultadoObtenerDatosFechaAdmisionProgramaDoctorado_", para obtener también el periodo académico
```sql
SELECT sol.fechaadmision AS fechaAdmisionProgramaDoctorado,p.nombre AS periodoAcadAdmision
FROM dot_solicitudadmisiondoctorado sol
INNER JOIN alumno a ON a.id = sol.alumno_id
INNER JOIN ofertaformativa ofe ON ofe.id = sol.ofertaformativasolicitada_id
INNER JOIN expediente e ON e.persona_idtercero = a.persona_idtercero
INNER JOIN expedienteespecifico ee ON ee.expedientegeneral_id = e.id
INNER JOIN expedienteacademico ea ON ea.id = ee.id and ea.ofertaformativa_id = ofe.id
INNER JOIN periodoacademico p on p.id = sol.periodoacademico_id
WHERE sol.estadosolicitudadmisiondoctorado = 'ADMITIDA'
AND ee.id = :idExpedienteAcademico
```

```sql
<sql-result-set-mapping name="ResultadoObtenerDatosFechaAdmisionProgramaDoctorado">
	<column-result name="fechaAdmisionProgramaDoctorado" />
	<column-result name="periodoAcadAdmision" />
</sql-result-set-mapping>
```

* Se a modificado el método _obtenerDatosFechaAdmisionDoctorado_ para tratar el nuevo campo , en la clase **JpaExpedienteAcademicoDao.java** en el método **transformarObtenerDatosFechaAdmisionDoctorado** es donde vamos a tratar el nuevo valor que hemos obtenido en la consulta
```java
/**
* Transforma los resultados obtenidos por la query nativa OBTENER_DATOS_FECHA_ADMISION_PROGRAMA_DOCTORADO.
*
* @param results el result-set de la query nativa
* @return el VO DatosInformeFechaAdmisionDoctoradoVO.
*/
private DatosInformeFechaAdmisionDoctoradoVO transformarObtenerDatosFechaAdmisionDoctorado(final List<Object[]> results) {

final DatosInformeFechaAdmisionDoctoradoVO datosfechaAdmisionDoctorado = new      DatosInformeFechaAdmisionDoctoradoVO();

	if ((results != null) && !results.isEmpty()) {
		Object[] resultadosConsulta = results.get(0);
		Date fechaAdmisionDoctorado = DaoUtils.getDate(resultadosConsulta[0]);
		datosfechaAdmisionDoctorado.setFechaAdmision(fechaAdmisionDoctorado);
		String periodoAcadAdmision =DaoUtils.getString(resultadosConsulta[1]);
		datosfechaAdmisionDoctorado.setPeriodoAcadAdmision(periodoAcadAdmision);
}
return datosfechaAdmisionDoctorado;
```

* En la clase **DefaultInformesExpService.java**  debemos de de inyectarlo al informe para que nos muestre los datos , para ello dentro de este método **generarPdfConSituacionAcademicaDoctorado** , ponemos lo siguiente
```java
final DatosInformeFechaAdmisionDoctoradoVO datosFechaAdmisionDoctorado = this
				.obtenerDatosFechaAdmisionDoctorado(vo.getExpedienteAcademico());
		informe.addDatosFechaAdmision(datosFechaAdmisionDoctorado);

```

* Ahora nos faltaría la parte de mostrar ese nueva campo en el informe,  debemos crear un nuevo delta para insertar la etiqueta en base de datos , para que luego desde java coger este valor y cargar su contenido.

* En la clase **DefaultInformesExpService.java**  en el metodo **cargarTextosFijosDoctorado** debemos de crear uno nuevo método para cargar ese valor  **cargarTextoCursoAcademicoIngresoDoctoradoFromProperties** 
```java
pars = this.cargarTextoCursoAcademicoIngresoDoctoradoFromProperties(pars, this.selectedCAProperties);
```

```java
/**
* Carga el texto de la
*
* @param pars
* Map donde se deben cargar los valores
* @param languageProperties
* Propiedades de idioma
* @return Map cargado con los valores
*/
private Map<String, Object> cargarTextoCursoAcademicoIngresoDoctoradoFromProperties(final Map<String, Object> pars,

	final Properties languageProperties) 

	//campo Nuevo
	pars.put("DOC_CURSO_ACADEMICO_INGRESO",    SiesStringUtils.getValueFromProperties(languageProperties,
ConstantesCaProperties.DOC_CURSO_ACADEMICO_INGRESO));
return pars;
}
```

* Este será el delta para crearlo en base de datos **20231219_insert_configuration_DOC_NOMBRE_CURSO_ACEDEMICO_INGRESO.sql**

* Luego a nivel de iReport **datosFechaAdmisionDoctorado.jrxml** se debe crear un nuevo parameters y un fields .
![[Pasted image 20240102144334.png]]

* También debemos de crear un parámetro a nivel de ireport padre , que será el que englobe los subreport **situacionAcademicaDoctorado.jrxml**
*![[Pasted image 20240102144159.png]]

