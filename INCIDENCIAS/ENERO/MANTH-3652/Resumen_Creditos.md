## Cambios que nos Piden

* Sería hacer lo mismo que se pide en el punto 5 pero esta vez para “**Premios**”.
## Clases Modificadas

* En la clase dentro del método **generarPdfConCertificadoAcademicoDoctorado**  se ha comentado este fragmento de código para que no se active los check.
 ```java
// Datos Acceso.
// if (vo.getIncluirDatosAcceso()) {
//
// final DatosInformeModalidadVo datosModalidad = this.obtenerDatosModalidad(vo.getLanguage(),
// vo.getExpedienteAcademico());
//
// informe.addDatosAcceso(datosModalidad);
// }
//
```

```java
	if (vo.getIncluirDatosAcceso()) {final DatosInformeModalidadVo datosModalidad =         this.obtenerDatosModalidad(vo.getLanguage(),vo.getExpedienteAcademico());
informe.addDatosAcceso(datosModalidad);
```

* Después debemos ir al RCP a la clase **GenerarCertificadoSinSolicitudDialogForm.java** , y comentar varias líneas para deshabilitar los check .
 ```java
private void habilitarCamposCertificadoAcademicoDoctorado() {
// this.jDatosAcceso.setEnabled(Boolean.TRUE);
this.jObservaciones.setEnabled(Boolean.TRUE);
this.jObservacionesText.setEnabled(Boolean.TRUE);
//this.jPremios.setEnabled(Boolean.TRUE);
this.jNpp.setEnabled(Boolean.TRUE);
this.jTitulo.setEnabled(Boolean.TRUE);
}
```

```java
private void establecerValoresPorDefectoCertificadoAcademicoDoctorado() {

// Establecemos los valores en los componentes.
// ((JCheckBox) this.jDatosAcceso).setSelected(Boolean.TRUE);
((JCheckBox) this.jObservaciones).setSelected(Boolean.TRUE);
// ((JCheckBox) this.jPremios).setSelected(Boolean.TRUE);
((JCheckBox) this.jNpp).setSelected(Boolean.TRUE);
((JCheckBox) this.jTitulo).setSelected(Boolean.TRUE);

// Establecemos los valores en los VM.
// DirtyTrackingUtils.setValueWithoutTrackDirty(this.getFormModel().getValueModel(VM_INCLUIR_DATOS_ACCESO),
// Boolean.TRUE);
DirtyTrackingUtils.setValueWithoutTrackDirty(this.getFormModel().getValueModel(VM_INLUIR_OBSERVACIONES),
Boolean.TRUE);
// DirtyTrackingUtils.setValueWithoutTrackDirty(this.getFormModel().getValueModel(VM_INCLUIR_PREMIOS),
// Boolean.TRUE);
DirtyTrackingUtils.setValueWithoutTrackDirty(this.getFormModel().getValueModel(VM_INCLUIR_NPP), Boolean.TRUE);
DirtyTrackingUtils
.setValueWithoutTrackDirty(this.getFormModel().getValueModel(VM_INCLUIR_TITULO), Boolean.TRUE);
}
```