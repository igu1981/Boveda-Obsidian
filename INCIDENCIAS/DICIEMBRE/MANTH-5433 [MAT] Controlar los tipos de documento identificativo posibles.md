---
tags:
  - MAT
link: 
Fecha Asignada: 2023-12-19T00:00:00
Despliegue: EX-1676
---

> [!info]- Cambios a realizar
 >-  A la hora de crear una matrícula de un alumno desconocido (Botón _Nueva_ en la Solicitud de Matrícula), en la pestaña de Datos Personales hay que indicar los datos del nuevo alumno. Ocurre que en el desplegable del Tipo de Documento aparecen tipos que no son aplicables a personas físicas. Hay que limitar estos combos para que muestren únicamente los tipos posibles.
   >-  Estoy hay que hacerlo en: (*Matrícula de Grado/Máster* , *Matrícula de Máster Erasmus Mundus* , *Matrícula No Ordinaria* ,*Matrícula de Doctorado*
   >- Hay que mostrar solamente los tipos de documento posibles y además en un orden lógico. Para ello existe el método siguiente: 
                *TipoDocumentoHelper.bindingTiposPersonasFisicas*
 Este método ya crea el binding con el ComboBox listo para usar en cualquier formulario.

## Clases Modificadas
- En la clase _SolicitudMatriculaDatosPersonalesChildForm.java_ hemos sustituido el combo que había de Tipo de Documento por el nuevo
```java
//antes
.row(TipoDocumentoHelper.bindingTiposPersonasFisicas(this, TIPO_DOCUMENTO_IDENTIFICATIVO),
						VALUE_MODEL_NUMERO_DOCUMENTO_IDENTIFICATIVO, bf.createBoundComboBox("paisExpedicionDocumento",
								refreshablePaisesValueHolder, NOMBRE))
```
