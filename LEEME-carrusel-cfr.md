# Carrusel de formacións · CFR de Vigo — Guía de uso

Carrusel de tarxetas que mostra as formacións en prazo de inscrición, embebido na aula virtual de Moodle do CFR de Vigo. É unha páxina web (`index.html`) publicada en GitHub Pages que se conecta automaticamente a follas de Google Sheets.

URL en produción: `https://caixadocorreo.github.io/carrusel-cfr/`

---

## 1. Como se integra en Moodle

Na etiqueta da aula virtual onde se quere mostrar o carrusel, pégase este código en modo HTML (`<>`):

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

O script recibe a altura real do carrusel e axusta automaticamente o iframe, polo que non queda espazo en branco nin se corta contido, independentemente do número de relatores ou do tamaño de pantalla.

---

## 2. Que mostra e cando

Cada tarxeta amosa unha formación que está **en prazo de inscrición** segundo as datas configuradas. O carrusel avanza automaticamente cada 8 segundos, con botóns de anterior/seguinte e indicadores (puntos) para navegar manualmente.

Unha tarxeta de formación ten:
- Cabeceira (cor configurable): logo CFR · código e título · logo Xacobeo
- Columna esquerda (35%, mesma cor que a cabeceira): datas, horario, lugar e relator/a(s)
- Columna dereita (65%): imaxe alusiva á formación, ou texto "+info proximamente" se non hai imaxe
- Banner inferior cunha nota fixa sobre posibles cambios de horario

---

## 3. Fonte de datos principal — folla "Carrusel"

Publicada como CSV (`CSV_URL` no código). Columnas, por orde:

| Columna | Campo | Contido |
|---|---|---|
| A | `DataInicioVisual` | Data desde a que se mostra a tarxeta (DD/MM/AAAA) |
| B | `Codigo` | Código da actividade |
| C | `Titulo` | Título da formación |
| D | `DataInicio` | Data de inicio da actividade |
| E | `DataFin` | Data de fin da actividade |
| F | `HoraInicio` | Hora de inicio (HH:MM) |
| G | `HoraFin` | Hora de fin (HH:MM) |
| H | `Lugar` | Lugar de celebración |
| I | `Relatores` | Relator/es, separados por `;` |
| J | `ImagenURL` | URL da imaxe (ver sección 5) |
| K | `CorFondo` | Cor hexadecimal da cabeceira e columna esquerda (ex: `#1a5276`). Se está baleiro, usa o azul corporativo `#007bc4` |
| L | `Tipo` | `formacion` (ou baleiro), `portada`, `contraportada` ou `promo` — ver sección 4 |

### Regra de visualización das formacións normais (`Tipo=formacion` ou baleiro)
Unha formación móstrase desde `DataInicioVisual` (inclusive) até o día **seguinte** a `DataInicio` (sen incluír). É dicir, deixa de mostrarse automaticamente cando comeza a actividade.

---

## 4. Portadas, contraportadas e promocións (`Tipo`)

| `Tipo` | Posición no carrusel | Banner inferior |
|---|---|---|
| `portada` | Sempre a primeira tarxeta | Non |
| `contraportada` | Sempre a última tarxeta | Non |
| `promo` | Posición aleatoria entre as formacións (cambia cada vez que se carga a páxina) | Si, en azul corporativo |
| `formacion` / baleiro | Tarxeta normal | Si, na cor configurada en `CorFondo` |

Para `portada`, `contraportada` e `promo` só fan falta os campos `Tipo`, `ImagenURL`, `DataInicioVisual` e `DataFin`; o resto pódense deixar baleiros.

### Regra de visualización para portada/contraportada/promo
Móstranse desde `DataInicioVisual` (inclusive) até o día **seguinte** a `DataFin` (sen incluír). Se `DataFin` está baleira, móstranse desde `DataInicioVisual` en adiante sen data de remate.

### Imaxe recomendada
Proporción **16:9** (ex: 1200×675 px), para que se vexa correctamente tanto en móbil como en escritorio.

### Reutilizar unha portada/promo máis tarde
Non é preciso borrar a fila. Pon `DataFin` no pasado para que deixe de mostrarse, e cando queiras reactivala, actualiza `DataInicioVisual`/`DataFin` ás novas datas. Podes ter varias filas do mesmo `Tipo` con datas correlativas para ir rotando contidos.

---

## 5. Imaxes desde Google Drive

No campo `ImagenURL` pódese pegar directamente o enlace normal de "Compartir" de Google Drive (`https://drive.google.com/file/d/ID/view?usp=...`). O sistema convérteo automaticamente ao formato `https://lh3.googleusercontent.com/d/ID`, que funciona sen problemas de CORS dentro do iframe.

**Importante**: o ficheiro en Drive ten que estar compartido como "Calquera persoa coa ligazón" para que a imaxe sexa visible.

---

## 6. Banner inferior con mensaxes (ticker)

Franxa con texto en movemento continuo (dereita → esquerda), branco negrita, que aparece en todas as tarxetas excepto en portada e contraportada.

- **Formacións**: fondo da mesma cor que `CorFondo` da tarxeta
- **Promos**: fondo azul corporativo

A velocidade é constante (proporcional á lonxitude total do texto) e o movemento páuse ao pasar o rato por riba ou ao enfocar con teclado (accesibilidade), e respecta a preferencia do sistema "reducir movemento".

### Fonte de datos — folla "Frases" (`CSV_FRASES_URL`)
| Columna | Campo | Contido |
|---|---|---|
| A | `Texto` | Frase a mostrar |
| B | `Activo` | `Si`/`Non` — se é `Non`, a frase non se mostra (sen necesidade de borrala) |

Todas as frases activas concaténanse e rotan en bucle continuo na mesma secuencia para todo o carrusel.

---

## 7. Resumo de ligazóns CSV configuradas

No código (`index.html`), atópanse dúas constantes coas URLs dos CSV publicados:

- `CSV_URL` → folla "Carrusel" (formacións, portadas, contraportadas, promos)
- `CSV_FRASES_URL` → folla "Frases" (mensaxes do banner)

Se algunha folla cambia de URL de publicación, hai que actualizar a constante correspondente no código e volver subir o ficheiro a GitHub.

---

## 8. Actualización do sistema

Para cambios no deseño, lóxica ou estrutura (non só contido):

1. Editar o ficheiro `index.html`
2. Subir a GitHub no repositorio `caixadocorreo/carrusel-cfr`, substituíndo o ficheiro existente (Add file → Upload files)
3. A páxina publicada en GitHub Pages actualízase automaticamente uns minutos despois

Para cambios de **contido** (novas formacións, imaxes, frases, cores, portadas/promos) **non hai que tocar o código** — só editar as follas de Google Sheets "Carrusel" e "Frases".
