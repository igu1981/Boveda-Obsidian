---
🗄️Implemetacion: " RCP"
✏️created: 2024-03-08
tags:
  - Uniovi
  - Java
---
 ---

# ❗Información

Tenemos varias maneras para crear un Get y un Set en el proyecto:
1) Get y Set en el Rcp en un VO
2) Get y Set en el Rcp en un Bean
3) Get y Set en el Rcp en una clase de Dominio
Aquí explicaremos las tres maneras que tenemos de crear un Get y Set.

# **Get y Set en el Rcp en un VO**

---

Se declara el nuevo campo

```java
private String nombreItinerario
```

y se crea el get y set de ese campo
```java
public String getNombreItinerario() {
	return nombreItinerario;
	}

public void setNombreItinerario(String nombreItinerario) {
	this.nombreItinerario = nombreItinerario;
	}
```
# **Get y Set en el Rcp en un Bean**
---
Dentro de este tipo de Get y Set nos podemos encontrar dos tipos , todo esto dependerá de si la clase tiene una **implements**  o tiene **extends**

1) Para extends
    
```java
CausaAnulacionCertificadosBean extends DomainEntityBean<CausaAnulacionCert>
```

```java
private String codigo
```

```java
public String getCodigo() {
	if (this.theDomainObject != null) {
		return this.theDomainObject.getCodigo();
	}
	return null;
}

public void setCodigo(String codigo) {
	if (this.theDomainObject != null) {
		this.theDomainObject.setCodigo(codigo);
	}
}
```

1) Para implements

```java
CartaPagoBean implements Serializable
```

```java
private Date fechaSolicitud
```


```java
public Date getFechaSolicitud() {
	return fechaSolicitud;
}

public void setFechaSolicitud(Date fechaSolicitud) {
	this.fechaSolicitud = fechaSolicitud;
}
```

# **Get y Set en el Rcp en una clase de Dominio**

```java
@Column
private Date fechaSolicitud
```


```java
public Date getFechaSolicitud() {
	return fechaSolicitud;
}

public void setFechaSolicitud(Date fechaSolicitud) {
	this.fechaSolicitud = fechaSolicitud;
}
---

## 🌐 Alternativas 
