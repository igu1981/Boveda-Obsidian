---
tags:
  - Java
  - SERVICE
Fecha Asignada: 2023-11-20T00:00:00
---
---

## DESCRIPCION DE LO QUE NOS PIDEN

Debemos de crear una clase de dominio que implemente Auditoria

---

## PASOS A SEGUIR

Debemos de añadir las siguientes implementaciones

```java
	  DomainEntity<Long>
      Auditable<Long>, 
      AuditDataSettable 
```

```java
@Entity(name="es.uniovi.sies.ayu.domain.DatosAcademicos")
@EntityListeners(value = { AuditInterceptor.class })
@Table(name = "ayu_datosacademicos")
public class DatosAcademicos extends AbstractDomainEntity<Long> implements DomainEntity<Long>, Auditable<Long>, AuditDataSettable {
```

Dentro de la clase debemos de poner la siguiente propiedad ya que las otras **DomainEntity** ,**AuditDataSettable**  dos no necesitan ser declaradas ni tener get y set
```java
/**
* La información de auditoría.
*/
@Embedded
private AuditData auditData = new AuditData();
```

Con su get y set correspondiente 
```java
	  /**
     * Obtiene la información de auditoría (si no existe la crea).
     * @return la información de auditoría
     * @see es.uniovi.security.audit.Auditable#getAuditData()
     */
    public AuditData getAuditData() {
        if (this.auditData == null) {
            this.setAuditData(new AuditData());
        }
        return this.auditData;
    }

    /**
     * @param auditData
     *            the auditData to set
     */
    private void setAuditData(AuditData auditData) {
        this.auditData = auditData;
    }
```

El delta que creemos parar crear l nueva tabla en base de datos , deberá de tener estos campos para **AuditData**

```sql
create table 'informix'.xxxxxxx (
id serial8 not null primary key,
fecha_creacion datetime year to fraction(5),
fecha_modificacion datetime year to fraction(5),
__uuid__ varchar(36) not null,
usuario_creacion varchar(255),
usuario_modificacion varchar(255),
);
```


Ejemplo completo de delta  para la creación de una entidad Auditable #SQL 

```sql
create table 'informix'.configuracionmatriculaportipoofe (
id serial8 not null primary key,
fecha_creacion datetime year to fraction(5),
fecha_modificacion datetime year to fraction(5),
__uuid__ varchar(36) not null,
usuario_creacion varchar(255),
usuario_modificacion varchar(255),
tipo_oferta_formativa_id int8,
periodo_academico_id int8,
configuraciongenericapagosmultiples_id int8
);

alter table 'informix'.configuracionmatriculaportipoofe add constraint foreign key (tipo_oferta_formativa_id)
references 'informix'.tipoofertaformativa(codigo) ;

alter table 'informix'.configuracionmatriculaportipoofe add constraint foreign key(periodo_academico_id)
references 'informix'.periodoacademico(id) ;
```