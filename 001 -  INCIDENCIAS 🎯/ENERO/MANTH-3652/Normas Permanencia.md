## Cambios que nos Piden

* Ahora no se está mostrando, aunque se haya marcado el check “Control NPP”, porque se está usando el método que se usa para los certificados de Grado/Máster:  

```java
	private DatosPermanenciaProgresoVo _obtenerDatosNormasPermanenciaProgreso_(final ExpedienteAcademico ea)  
```

* Pero **este método no sirve para Doctorado** porque en doctorado no hay normas de progreso y las normas de permanencia no son las mismas que las de Grado/Máster.

* Necesitamos **implementar un nuevo método** para obtener la información de las npp de doctorado:

```java
List<NormasPermanenciaDoctoraoVo> obtenerDatosNormasPermanenciaDoctorado (idExpedienteAcademico)  
```

* Creamos un nuevo vo **NormasPermanenciaDoctoraoVo.java** con los siguientes campos (periodoAcademico, estado y descripcion_estado)

Consulta  **obtenerNormasPermanenciaExpedienteDoctorado** para obtener la información de las npp de un expediente de doctorado:

```sql
select pa.nombre as periodoAcademico,ca.estado as estado, ca.descripcion as descripcion_estado
from npp_alumnoexcluido ae
inner join npp_alumnoexcluido_npp_causaexclusionalumno aece on ae.id = aece.npp_alumnoexcluido_id
inner join npp_causaexclusionalumno ca on ca.id = aece.causasexclusion_id
inner join periodoacademico pa on ca.periodoacademico_id = pa.id
inner join expediente e on ae.alumno_idtercero = e.persona_idtercero
inner join expedienteespecifico ee on e.id = ee.expedientegeneral_id
inner join expedienteacademico ea on ee.id = ea.id and ea.ofertaformativa_id = ae.ofertaformativa_id
where ea.id = :idExpedienteAcademico
and ca.causaexclusion = 'NSEG'
order by pa.nombre asc
```

## Clases Modificadas

* En la clase **JpaExpedienteAcademicoDao.java** dentro del método , se ha creado este nuevo método con su transformación de datos correspondiente

```java
public List<NormasPermanenciaDoctoraoVo> obtenerDatosNormasPermanenciaDoctorado(Long idExpedienteAcademico) {

final EntityManager em = JpaUtil.getEntityManager(this.getJpaTemplate().getEntityManagerFactory());
final javax.persistence.Query query =
em.createNamedQuery(JpaExpedienteAcademicoDao.OBTENER_NORMAS_PERMANENCIA_EXPEDIENTE_DOCTORADO);
query.setParameter("idExpedienteAcademico", idExpedienteAcademico);
final List<NormasPermanenciaDoctoraoVo> listaNormasPermanencia = query.getResultList();
return transformarDatosNormasPermanenciaDoctorado(query.getResultList());
}
```

```java
private List<NormasPermanenciaDoctoraoVo> transformarDatosNormasPermanenciaDoctorado(final List<Object[]> result) {

List<NormasPermanenciaDoctoraoVo> normas = new ArrayList<>();
final int PERIODO_ACADEMICO = 0;
final int ESTADO = 1;
final int DESCRIPCION_ESTADO= 2;

for (Object[] fila : result) {
	String periodo = DaoUtils.getString(fila[PERIODO_ACADEMICO]);
	String estado = DaoUtils.getString(fila[ESTADO]);
	String descripcion = DaoUtils.getString(fila[DESCRIPCION_ESTADO]);
	PeriodoAcademico periodoAcademico = new PeriodoAcademico();
	periodoAcademico.setNombre(periodo);
	NormasPermanenciaDoctoraoVo normasVo = new NormasPermanenciaDoctoraoVo(periodoAcademico, estado, descripcion);
normas.add(normasVo);

}
return normas;
}

```

* Y hemos añadido esta consulta en el **orm.xml** con el nombre  de **obtenerNormasPermanenciaExpedienteDoctorado**

```sql
<named-native-query name="obtenerNormasPermanenciaExpedienteDoctorado"
result-set-mapping="ResultadoObtenerNormasPermanenciaExpedienteDoctorado">
<query>
<![CDATA[
	select pa.nombre as periodoAcademico,ca.estado as estado, ca.descripcion as descripcion_estado
	from npp_alumnoexcluido ae
	inner join npp_alumnoexcluido_npp_causaexclusionalumno aece on ae.id = aece.npp_alumnoexcluido_id
	inner join npp_causaexclusionalumno ca on ca.id = aece.causasexclusion_id
	inner join periodoacademico pa on ca.periodoacademico_id = pa.id
	inner join expediente e on ae.alumno_idtercero = e.persona_idtercero
	inner join expedienteespecifico ee on e.id = ee.expedientegeneral_id
	inner join expedienteacademico ea on ee.id = ea.id and ea.ofertaformativa_id = ae.ofertaformativa_id
	where ea.id = :idExpedienteAcademico
	and ca.causaexclusion = 'NSEG'
	order by pa.nombre asc
]]>
</query>
</named-native-query>
```

```sql
<sql-result-set-mapping name="ResultadoObtenerNormasPermanenciaExpedienteDoctorado">
		<column-result name="periodoAcademico" />
		<column-result name="estado" />
		<column-result name="descripcion_estado" />
</sql-result-set-mapping>
```


* Ahora en la clase **DefaultInformesExpService.java** dentro del método **generarPdfConCertificado** debemos de llamar a este método para obtener las normas de permanencia y pasárselo al informe  **WrapperInforme.java**

```java
         /*
		 * NORMAS DE PERMANENCIA Y PROGRESO
		 */
		if (vo.getIncluirNPP()) {
			final DatosPermanenciaProgresoVo npp = this
					.obtenerDatosNormasPermanenciaProgreso(vo.getExpedienteAcademico());
			/**
			 * Consulta nueva de NPP
			 */
			List<NormasPermanenciaDoctoraoVo> obtenerDatosNormasPermanenciaDoctorado = new ArrayList<NormasPermanenciaDoctoraoVo>();
			
			obtenerDatosNormasPermanenciaDoctorado = this.obtenerDatosNormasPermanenciaDoctorado(vo.getExpedienteAcademico().getId());

			if (npp != null) {
				informe.addNormasPermanenciaProgreso(npp);
			}
		}
```

```java
private List<NormasPermanenciaDoctoraoVo> obtenerDatosNormasPermanenciaDoctorado(Long idExpedienteAcademico) {
		if(idExpedienteAcademico != null) {
			return expedientesAcademicosDao.obtenerDatosNormasPermanenciaDoctorado(idExpedienteAcademico);
		}
		return null;
	}
```

* También dentro de este método **generarPdfConSituacionAcademicaDoctorado** 
```java
if (vo.getIncluirNPP()) {
			DatosPermanenciaProgresoVo npp = obtenerDatosNormasPermanenciaProgreso(vo.getExpedienteAcademico());
			
			List<NormasPermanenciaDoctoraoVo> nppLista = obtenerDatosNormasPermanenciaDoctorado(vo.getExpedienteAcademico().getId());
			System.out.println(nppLista.size());
			System.out.println(nppLista);
			npp = new DatosPermanenciaProgresoVo();
			npp.setNormasPermanenciaDoctorao(nppLista);
			

			if (npp != null) {
				informe.addNormasPermanenciaProgreso(npp);
			}
		}
```

* En el vo **DatosPermanenciaProgresoVo.java**  debemos de crear una lista que se llamara **normasPermanenciaDoctorao** que será la que recoja los datos que se vana mostrar en el informe

```java
private List<NormasPermanenciaDoctoraoVo> normasPermanenciaDoctorao = new ArrayList<NormasPermanenciaDoctoraoVo>();
//get y set
public List<NormasPermanenciaDoctoraoVo> getNormasPermanenciaDoctorao() {
	return normasPermanenciaDoctorao;
}
public void setNormasPermanenciaDoctorao(List<NormasPermanenciaDoctoraoVo> normasPermanenciaDoctorao) {
	this.normasPermanenciaDoctorao = normasPermanenciaDoctorao;
	for (NormasPermanenciaDoctoraoVo docVo : normasPermanenciaDoctorao) {
	NormasPermanenciaVo npOldVo = new NormasPermanenciaVo();
	npOldVo.setCurso(docVo.getPeriodoAcademico().getNombre());
	npOldVo.setCumplePermanecia(docVo.getEstadoCausaExclusion());
	List<String> motivosIncumplimiento = new ArrayList<String>();
	motivosIncumplimiento.add(docVo.getDescripcion());
	npOldVo.setMotivosIncumplimiento(motivosIncumplimiento);
	normasPermanencia.add(npOldVo);
	}
}
```
* En la clase **WrapperInforme.java** 
```java
public void addNormasPermanenciaProgreso(final DatosPermanenciaProgresoVo npp) {

	if (npp == null) {
		throw new IllegalArgumentException(
		"Las NPP es nula.");
	}

	if (this.normasPermanenciaProgreso == null) {
		this.normasPermanenciaProgreso = new ArrayList<DatosPermanenciaProgresoVo>();
	}

	this.normasPermanenciaProgreso.add(npp);
}
```

* De momento se está cogiendo los subReport que ya existían para pintar los nuevos datos. **normasPermanenciaDoctorado.jrxml**  y  **normasPermanenciaProgresoDoctorado.jrxml**
* 