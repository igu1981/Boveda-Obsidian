---
🗄️Implemetacion: " SERVICE"
✏️created: 2024-03-09
tags:
  - Uniovi
  - Java
---
 ---

# ❗Información

Vamos de presentar todos los tipos de datos que podemos meter en una entidad de dominio, esta clase java en la que mas se va aparecer a lo que tenemos en base de datos cuando creemos la entidad 
Lo dividiremos en dos secciones 
 1)  Campos Simples 
 2) Campos relacionados con otras tablas

## Campos Simples 
 Estos campos son valores que no tienen ninguna implicación con otras entidades , y los creamos  con una anotación de spring , privados y con su get y set correspondientes
 La anotación **@Column**  nos la podemos encontrar sin nada o con parámetros , uno de ellos e **name** que se utiliza para darle el nombre que se va a utilizar en base de datos para referirse a ellas.

 ```java
 // TIPOS DE CAMPOS SIMPLES
 //-----------------------------------------
 
@Column
private Date fechaSolicitud;

@Column
private String telefono;

@Column
private Long idItinerario;

@Column
private Boolean autoservicio = Boolean.FALSE;

@Column(name = "num_max_documentos")
private Integer maximoDocumentos;

```


## Campos relacionados con otras tablas
Estos campos son como los de arriba pero la única particularidad es qué estén relacionados con otras tablas o que son algo mas complejos.
A continuación veremos los que son mas frecuentes de encontrarse con ellos en las entidades de dominio

Campo que se utiliza como identificador  y se incremento automáticamente
 ```java
 /** Identificador autogenerado. */
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
```

Manera de referirse a un campo de tipo Enumerado
 ```java
@NotNull
@Enumerated(EnumType.STRING)
private ERolMiembroProgramaDoctorado rol;

```

Relación @ManyToOne
 ```java
@NotNull
@ManyToOne(fetch = FetchType.LAZY)
@org.hibernate.annotations.Fetch(value = org.hibernate.annotations.FetchMode.SELECT)
private ProgramaDoctorado programaDoctorado;
```

Relación @OneToMany
 ```java
@OneToMany(fetch = FetchType.LAZY, cascade = { CascadeType.ALL })
@org.hibernate.annotations.Cascade({ org.hibernate.annotations.CascadeType.DELETE_ORPHAN })
private Set<ElementoNoCursable> elementosNoCursables = new HashSet<ElementoNoCursable>();

```

Relación @OneToOne
 ```java
@OneToOne(fetch = FetchType.EAGER)
@JoinColumn(name = "acreditacionidiomagen_id")
private AcreditacionIdiomaAlumno acreditacionIdiomaAlumno;


public AcreditacionIdiomaAlumno getAcreditacionIdiomaAlumno() {
	return acreditacionIdiomaAlumno;
}

public void setAcreditacionIdiomaAlumno(AcreditacionIdiomaAlumno acreditacionIdiomaAlumno) {
	this.acreditacionIdiomaAlumno = acreditacionIdiomaAlumno;
}
```


La información de auditoría. Necesaria por implementar el interfaz **Auditable**
 ```java
@Embedded
private AuditData auditDataEA = new AuditData();


public AuditData getAuditDataEA() {
	return this.auditDataEA;
}
```


-