
## Cambios que nos Piden

## Clases Modificadas

- En la jasper **datosTesisDoctorado.jrxml**  en  la etiqueta y en la variable debemos de ponerle una condicion para que  solo debe aparecer este apartado si hay información de la cotutela.
- En el Parámetro **$P{DOC_TESIS_TEXT_COTUTELA}** hemos añadido una nueva condición en Print When Expression  

```xml

// etiqueta
new Boolean(!" - ".equals($F{universidadCotutela}))

//varibale
new Boolean(!" - ".equals($F{universidadCotutela})

```

- Tenemos este clase **DatosInformeTesisDoctoradoVO.java**  pero aquí no tenemos que hacer nada
```java

    /**
     * Devuelve el nombre de la Universidad que cotutela la tesis doctoral.
     * 
     * @return el nombre de la Universidad que cotutela la tesis doctoral.
     */
    public String getUniversidadCotutela() {
        final Universidad universidad = this.tesisDoctoral.getUniversidadCotutela();
        try {
            if ((universidad != null) && (universidad.getNombre() != null)) {
                final StringBuilder sb = new StringBuilder();
                sb.append(universidad.getNombre());
                return sb.toString();
            }
        } catch(final Exception e) {
            return SiesStringUtils.EXCEPTION_RETURN;
        }

        return SiesStringUtils.DEFAULT_RETURN;
    }
```