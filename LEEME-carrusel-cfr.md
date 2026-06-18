# Carrusel de formacións · CFR de Vigo — Guía de uso

Carrusel de tarxetas que mostra as formacións en prazo de inscrición, as sesións do día e contido informativo adicional, embebido na aula virtual de Moodle do CFR de Vigo. É unha páxina web (`index.html`) publicada en GitHub Pages que se conecta automaticamente a varias follas de Google Sheets.

URL en produción: `https://caixadocorreo.github.io/carrusel-cfr/`

---

## 1. Como se integra en Moodle

Na etiqueta da aula virtual, pégase este código en modo HTML (`<>`):

```html
<div style="width: 100%; margin: auto;">
<iframe id="carrusel-cfr" src="https://caixadocorreo.github.io/carrusel-cfr/" width="100%" frameborder="0" scrolling="no" style="border:none;"></iframe>
<script>
window.addEventListener('message', function(e) {
  if (e.data && e.data.tipo === 'alturaCarrusel') {
    document.getElementById('carrusel-cfr').style.height = e.data.altura + 'px';
  }
});
</script>
</div>
```

O script axusta automaticamente a altura do iframe ao contido real, sen espazo en branco nin contido cortado.

---

## 2. Que contén o carrusel e en que orde

O carrusel avanza automaticamente cada 8 segundos. A orde das tarxetas é:

1. **Portada** (se existe e está en prazo) — sempre a primeira
2. **Sesións de hoxe** (se hai sesións de tarde ese día) — inmediatamente despois da portada
3. **Formacións**, **promos** e **recomendacións de lectura** — intercaladas en posición aleatoria
4. **Contraportada** (se existe e está en prazo) — sempre a última

---

## 3. Tipos de tarxeta

### 3.1 Formacións (`Tipo=formacion` ou baleiro)
Tarxeta horizontal con:
- **Cabeceira** (cor configurable): logo CFR · código en negrita + título · logo Xacobeo
- **Columna esquerda** (35%, mesma cor): datas, horario, lugar, relator/a(s) + nota sobre posibles cambios de horario
- **Columna dereita** (65%): imaxe alusiva ou texto "+info proximamente"
- **Banner inferior** con frases rotativas

Móstrase desde `DataInicioVisual` até o día seguinte a `DataInicio`.

### 3.2 Sesións de hoxe
Tarxeta con:
- **Cabeceira azul** con logos, "Sesións de hoxe" + data + hora actual
- **Grid de 4 columnas** (15% aula / 35% info / 15% aula / 35% info) e 3 filas = 6 sesións por slide
- Se hai máis de 6 sesións, xéranse slides adicionais automaticamente
- Só se amosan sesións con hora de inicio ≥ 13:45

Fonte de datos: **folla de Sesións da cartelería TV** (CSV compartido).

### 3.3 Portada / Contraportada (`Tipo=portada` / `Tipo=contraportada`)
Imaxe a pantalla completa (proporción 16:9 recomendada). Sen banner inferior.

### 3.4 Promos (`Tipo=promo`)
Imaxe a pantalla completa intercalada en posición aleatoria. Banner inferior en azul corporativo.

### 3.5 Recomendacións de lectura
Tarxeta con:
- **Cabeceira azul** con logos, "+info" + data + hora actual
- **Columna esquerda** (40%): portada do libro con animación de "respiración" en bucle
- **Columna dereita** (60%): etiqueta "Recomendación de lectura" + título (azul, negrita) + autor (azul, negrita) + texto da recomendación (azul)

Intercaladas en posición aleatoria. Fonte de datos: **CSV compartido coa cartelería TV**.

---

## 4. Follas de Google Sheets

### Folla "Carrusel" (`CSV_URL`)
| Columna | Campo | Contido |
|---|---|---|
| A | `DataInicioVisual` | Data desde a que se mostra (DD/MM/AAAA) |
| B | `Codigo` | Código da actividade |
| C | `Titulo` | Título da formación |
| D | `DataInicio` | Data de inicio da actividade |
| E | `DataFin` | Data de fin da actividade |
| F | `HoraInicio` | Hora de inicio (HH:MM) |
| G | `HoraFin` | Hora de fin (HH:MM) |
| H | `Lugar` | Lugar de celebración |
| I | `Relatores` | Relator/es, separados por `;` |
| J | `ImagenURL` | URL da imaxe |
| K | `CorFondo` | Cor hex da cabeceira (ex: `#1a5276`). Baleiro = azul corporativo |
| L | `Tipo` | `formacion`, `portada`, `contraportada` ou `promo` |

### Folla "Sesións" (`CSV_SESIONS_URL`)
Compartida coa cartelería TV. Columnas: `Data`, `Aula`, `Código`, `Título`, `Relatores`, `HoraInicio`, `HoraFin`.

### Folla "Frases" (`CSV_FRASES_URL`)
| Columna | Campo | Contido |
|---|---|---|
| A | `Texto` | Frase a mostrar no banner |
| B | `Activo` | `Si`/`Non` |

### Folla "Lecturas" (`CSV_LECTURAS_URL`)
Compartida coa cartelería TV.
| Columna | Campo | Contido |
|---|---|---|
| A | `Titulo` | Título do libro |
| B | `Autor` | Autor/a |
| C | `Recomendacion` | Texto breve (2-3 liñas) |
| D | `ImagenURL` | Portada do libro (vertical, proporción 2:3) |
| E | `DataInicioVisual` | Data de inicio de visualización (DD/MM/AAAA) |
| F | `DataFin` | Data de fin de visualización (DD/MM/AAAA) |

---

## 5. Imaxes desde Google Drive

Pega o enlace normal de "Compartir" (`https://drive.google.com/file/d/ID/view?usp=...`). O sistema convérteo automaticamente ao formato `lh3.googleusercontent.com` que funciona sen problemas de CORS.

O ficheiro en Drive ten que estar compartido como **"Calquera persoa coa ligazón"**.

---

## 6. Banner inferior con mensaxes (ticker)

Franxa con texto en movemento continuo (dereita → esquerda), branco negrita:
- **Formacións**: cor de `CorFondo`
- **Promos e lecturas**: azul corporativo
- **Portada/contraportada**: sen banner

Páusase ao pasar o rato por riba (accesibilidade) e respecta `prefers-reduced-motion`.

---

## 7. Resumo de constantes CSV no código

| Constante | Folla |
|---|---|
| `CSV_URL` | Carrusel (formacións, portadas, promos...) |
| `CSV_SESIONS_URL` | Sesións TV (compartida) |
| `CSV_FRASES_URL` | Frases do banner |
| `CSV_LECTURAS_URL` | Lecturas (compartida coa cartelería TV) |

Se algunha folla cambia de URL de publicación, actualiza a constante no código e sube de novo a GitHub.

---

## 8. Actualización do sistema

Para cambios no **contido** (formacións, frases, lecturas, portadas/promos): editar as follas de Google Sheets — non hai que tocar o código.

Para cambios no **deseño ou lóxica**:
1. Editar o ficheiro `index.html`
2. Subir a GitHub en `caixadocorreo/carrusel-cfr` (Add file → Upload files)
3. GitHub Pages actualízase automaticamente en poucos minutos
