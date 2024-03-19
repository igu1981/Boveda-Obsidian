---
tags:
  - Java
  - SERVICE
Fecha Asignada: 2024-01-18T00:00:00
---
---

## DESCRIPCION DE LO QUE NOS PIDEN

Nos piden que debemos de elegir por defecto el primer campo del combo 

---

## PASOS A SEGUIR
Estos cambios los debemos de hacer en el #RCP 

```java
//escogemos el 1º porque si no escoge el vacío
JComponent formRet = formBuilder.getForm(); //necesario
List<PeriodoAcademico> periodos = (List<PeriodoAcademico>) periodoAcademicoRefreshable.getValue();
periodos.sort(comboBindingPeriodo.getComparator());
DirtyTrackingUtils.setValueWithoutTrackDirty(
getFormModel().getValueModel(VM_PERIODO_ACADEMICO),
periodos.get(0));
return new JScrollPane(formRet);
```

