# Apéndice: Transformaciones {-}




En ocasiones es conveniente transformar los datos para el análisis, el 
objetivo de los ajustes es simplificar la interpretación y el análisis al 
eliminar fuentes de variación conocidas, también es común realizan
transformaciones para simplificar los patrones.

Algunos ejemplos donde eliminamos efectos conocidos: 

1. Cuando analizamos el precio de venta de las casas podemos eliminar la variación 
debida al tamaño de las casas al pasar de precio de venta a precio de venta por metro cuadrado. 
De manera similar al analizar las propinas puede convenir considerar la propina como porcentaje de la cuenta.

2. En series de tiempo cuando los datos están relacionados con el tamaño de la 
población podemos ajustar a mediciones per capita (en series de tiempo PIB). También es común ajustar por inflación, o poner cantidades monetarias en valor presente.


``` r
mex_dat <- global_economy |> 
  filter(Code == "MEX")

pib <- ggplot(mex_dat, aes(x = Year, y = GDP / 1e6)) +
  geom_line()

pib_pc <- ggplot(mex_dat, aes(x = Year, y = GDP / Population)) +
  geom_line()

pib / pib_pc
```

<img src="89-transformaciones_files/figure-html/unnamed-chunk-2-1.png" width="576" />


Adicionalmente podemos recurrir a otras transformaciones matemáticas (e.g. logaritmo, raíz cuadrada) que simplifiquen
el patrón en los datos y la interpretación. 

Veamos un ejemplo donde es apropiado la transformación logaritmo. 

Usamos los datos Animals con información de peso corporal promedio y peso cerebral
promedio para 28 especies. Buscamos entender la relación entre estas dos
variables, e inspeccionar que especies se desvían (residuales) del esperado. 
Comenzamos con un diagrama de dispersión usando las unidades originales


``` r
animals_tbl <- as_tibble(Animals, rownames = "animal")

p1 <- ggplot(animals_tbl, aes(x = body, y = brain, label = animal)) +
  geom_point() + 
  labs(subtitle = "Unidades originales")

p2 <- ggplot(animals_tbl, aes(x = body, y = brain, label = animal)) +
  geom_point() + xlim(0, 500) + ylim(0, 1500) +
  geom_text_repel() + 
  labs(subtitle = "Unidades originales, eliminando 'grandes'")

(p1 + p2)
```

<img src="89-transformaciones_files/figure-html/unnamed-chunk-3-1.png" width="672" />

Incluso cuando nos limitamos a especies de menos de 500 kg de masa corporal, la 
relación no es fácil de descrubir.En la suguiente gráfica hacemos la transformación 
logaritmo y obtenemos una gráfica más fácil de 
leer, además los datos se modelarán con más facilidad.


``` r
p3 <- ggplot(animals_tbl, aes(x = log(body), y = log(brain), label = animal)) +
  geom_smooth(method = "lm", se = FALSE, color = "red") +
  geom_point() + 
  geom_text_repel()  + 
  stat_poly_eq(use_label(c("eq"))) 

p3
```

```
## `geom_smooth()` using formula = 'y ~ x'
```

<img src="89-transformaciones_files/figure-html/unnamed-chunk-4-1.png" width="672" />

La transformación logaritmo tiene también ventajas en interpretación, para diferencias
chicas en escala log, las diferencias corresponden a diferencias porcentuales
en la escala original, por ejempo consideremos la diferencia entre el peso en escala
log de humano y borrego: 4.13 - 4.02 = 0.11. 
Confirmamos que el humano es aproximadamente 11% más pesado que el borrego en la 
escala original: 62/55.5 - 1 = 0.12



``` r
animals_tbl <- animals_tbl |>  
  mutate(log_body = log(body), 
         log_brain = log(brain)) 

animals_tbl |> 
  filter(animal == "Human" | animal == "Sheep") |> 
  arrange(body) |> 
  gt::gt() |> 
  gt::fmt_number()
```

```{=html}
<div id="ociqvanemt" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#ociqvanemt table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#ociqvanemt thead, #ociqvanemt tbody, #ociqvanemt tfoot, #ociqvanemt tr, #ociqvanemt td, #ociqvanemt th {
  border-style: none;
}

#ociqvanemt p {
  margin: 0;
  padding: 0;
}

#ociqvanemt .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#ociqvanemt .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#ociqvanemt .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#ociqvanemt .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#ociqvanemt .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#ociqvanemt .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ociqvanemt .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#ociqvanemt .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#ociqvanemt .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#ociqvanemt .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#ociqvanemt .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#ociqvanemt .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#ociqvanemt .gt_spanner_row {
  border-bottom-style: hidden;
}

#ociqvanemt .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#ociqvanemt .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#ociqvanemt .gt_from_md > :first-child {
  margin-top: 0;
}

#ociqvanemt .gt_from_md > :last-child {
  margin-bottom: 0;
}

#ociqvanemt .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#ociqvanemt .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#ociqvanemt .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#ociqvanemt .gt_row_group_first td {
  border-top-width: 2px;
}

#ociqvanemt .gt_row_group_first th {
  border-top-width: 2px;
}

#ociqvanemt .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#ociqvanemt .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#ociqvanemt .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#ociqvanemt .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ociqvanemt .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#ociqvanemt .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#ociqvanemt .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}

#ociqvanemt .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#ociqvanemt .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#ociqvanemt .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#ociqvanemt .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#ociqvanemt .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#ociqvanemt .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#ociqvanemt .gt_left {
  text-align: left;
}

#ociqvanemt .gt_center {
  text-align: center;
}

#ociqvanemt .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#ociqvanemt .gt_font_normal {
  font-weight: normal;
}

#ociqvanemt .gt_font_bold {
  font-weight: bold;
}

#ociqvanemt .gt_font_italic {
  font-style: italic;
}

#ociqvanemt .gt_super {
  font-size: 65%;
}

#ociqvanemt .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}

#ociqvanemt .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#ociqvanemt .gt_indent_1 {
  text-indent: 5px;
}

#ociqvanemt .gt_indent_2 {
  text-indent: 10px;
}

#ociqvanemt .gt_indent_3 {
  text-indent: 15px;
}

#ociqvanemt .gt_indent_4 {
  text-indent: 20px;
}

#ociqvanemt .gt_indent_5 {
  text-indent: 25px;
}

#ociqvanemt .katex-display {
  display: inline-flex !important;
  margin-bottom: 0.75em !important;
}

#ociqvanemt div.Reactable > div.rt-table > div.rt-thead > div.rt-tr.rt-tr-group-header > div.rt-th-group:after {
  height: 0px !important;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <thead>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1" scope="col" id="animal">animal</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="body">body</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="brain">brain</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="log_body">log_body</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1" scope="col" id="log_brain">log_brain</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="animal" class="gt_row gt_left">Sheep</td>
<td headers="body" class="gt_row gt_right">55.50</td>
<td headers="brain" class="gt_row gt_right">175.00</td>
<td headers="log_body" class="gt_row gt_right">4.02</td>
<td headers="log_brain" class="gt_row gt_right">5.16</td></tr>
    <tr><td headers="animal" class="gt_row gt_left">Human</td>
<td headers="body" class="gt_row gt_right">62.00</td>
<td headers="brain" class="gt_row gt_right">1,320.00</td>
<td headers="log_body" class="gt_row gt_right">4.13</td>
<td headers="log_brain" class="gt_row gt_right">7.19</td></tr>
  </tbody>
  
  
</table>
</div>
```






Y podemos usarlo también para interpretar la recta de referencia $y = 2.55 + 0.5 x$
, para cambios chicos:
*Un incremento de 10% en masa total corresponde en un incremento de 5% en masa cerebral.*

El coeficiente de la regresión log-log, en nuestro ejemplo 0.5, es la [elasticidad](https://en.wikipedia.org/wiki/Elasticity_(economics)) y es un concepto
común en economía.

**Justificación**

Para entender la interpretación como cambio porcentual recordemos primero 
que la representación con series de Taylor de la función exponencial es:


$$e^x = \sum_{n=0}^\infty \frac{x^n}{n!}$$

Más aún podemos tener una aproximación usando [polinomios de Taylor](https://en.wikipedia.org/wiki/Taylor%27s_theorem), en el caso de la 
exponencial el $k$-ésimo polinomio de Taylor está dado por:

$$e^\delta \approx 1 + \delta + \frac{1}{2!}\delta^2 + \dots +  \frac{1}{k!}\delta^k$$

y si $\delta$ es chica (digamos menor a 0.15), entonces la aproximación de primer grado es
razonable y tenemos:

$$Ae^{\delta} \approx A(1+\delta)$$


``` r
dat <- tibble(delta = seq(0, 1, 0.01), exp_delta = exp(delta), uno_mas_delta = 1 + delta)

ggplot(dat, aes(x = uno_mas_delta, y = exp_delta)) +
  geom_line() +
  geom_abline(color = "red") +
  annotate("text", x = 1.20, y = 1.18, label = "y = x", color = "red", size = 6)
```

<img src="89-transformaciones_files/figure-html/unnamed-chunk-7-1.png" width="432" />




