<script>
  import { onMount } from 'svelte';
  import mapboxgl from 'mapbox-gl';

  import us_states from '../src/assets/us_states3.json';


  let currentTab = 2000;
  let rangeValue = 0;

  const mapboxToken = 'pk.eyJ1IjoibGF1cjA1IiwiYSI6ImNpbmtmM2FjazAwODF2eG0yNjhteTcxdHIifQ.l7uzjVe2b1L8dHh_Z9JjoQ';

  // Define los años para el slider
  const years = [2000, 2004, 2008, 2012, 2016, 2020, 2024];

  import { electionData } from '../src/assets/electionData.js';

  const initialZoom = window.innerWidth <= 768 ? 2 : 3.5;

 

  let map;






  onMount(() => {






    map = new mapboxgl.Map({
      container: 'lv_map-container',
      style: 'mapbox://styles/mapbox/light-v10',
      center: [-98.5795, 39.8283],
      zoom: initialZoom,
      accessToken: mapboxToken,
      scrollZoom: false,
    });

    map.addControl(new mapboxgl.NavigationControl(), 'top-right');
    map.addControl(new mapboxgl.FullscreenControl(), 'top-right');




    map.on('load', () => {
      map.addSource('us_states', {
        type: 'geojson',
        data: us_states,
      });

      map.addLayer({
        id: 'state-fills',
        type: 'fill',
        source: 'us_states',
        layout: {},
        paint: {
          'fill-color': [
            'match',
            ['get', 'name'],
            ...Object.keys(electionData[currentTab]).flatMap(state => [
              state,
              electionData[currentTab][state]
            ]),
            'gray'
          ],
          'fill-opacity': 0.7
        }
      });




      map.addLayer({
        id: 'state-borders',
        type: 'line',
        source: 'us_states',
        layout: {},
        paint: {
          'line-color': '#fff', 
          'line-width': 1, 
          'line-opacity': 0.5 
        }
      });






    });
  });

  function updateMapSourceByRange(value) {
    const yearIndex = Math.round(value / (100 / (years.length - 1)));
    currentTab = years[yearIndex];
    if (map && map.getSource('us_states')) {
      map.setPaintProperty('state-fills', 'fill-color', [
        'match',
        ['get', 'name'],
        ...Object.keys(electionData[currentTab]).flatMap(state => [
          state,
          electionData[currentTab][state]
        ]),
        'gray'
      ]);
    }
  }
</script>






<main>

  <div class="lv_piece">

    <div class="lv_pieceWrapper">


      <div class="lv_caratula">
        <div class="lv_caratulaImg"></div>
        <div class="lv_caratulaTexts">
          <div class="lv_caratulaTextsWrapper">
            <h1>Así se ha votado en Estados Unidos desde el año 2000</h1>
            <div class="lv_separator"></div>
            <h4>Trump recupera los estados bisagra que Biden le había arrebatado en 2016 para apuntalar su retorno a la Casa Blanca    </h4>

          </div>



        </div>
      </div>











      <div class="lv_article">

        <div id="lv_Authors">

          <div class="lv_authorsWrapper">
              <span class="lv_authorName">
                  <a href="https://www.lavanguardia.com/autores/david-dusster.html" target="_blank">
                      <h4>David Dusster</h4>
    
                  </a>
              </span>
    
            
    
    
    
             
    
    
    
          </div>
    
          <div class="lv_xarxesWrapper">
                  <a href="" target="_blank" class="btn rounded-circle"><i class="fab fa-facebook-f"></i></a>
                  <a href="" target="_blank" class="btn rounded-circle"><i class="fab fa-twitter"></i></a>
                  <a href="" target="_blank" class="btn rounded-circle"><i class="fab fa-whatsapp"></i></a>
          </div>
    
        
    
        
        
        </div> <!-- END of authors + xxss -->





          <div class="lv_textBlock">
    
    
    
            <p class="lv_Par">Las encuestas vaticinaban un resultado tan ajustado entre Donald Trump y Kamala Harris que incluso se especulaba con que se tardaría días en conocer la identidad del nuevo presidente de los Estados Unidos. Nada más lejos de la realidad. La incertidumbre duró pocas horas. El magnate fue consolidando su victoria estado tras estado y fue el triunfo en Pensilvania, el de mayor peso electoral de los siete estados bisagra, el que acabó confirmando su retorno a la Casa Blanca. 
            </p>


            <p class="lv_Par">Se esperaban las presidenciales más reñidas del siglo XXI desde aquel duelo entre Bush hijo y Al Gore que terminó resolviéndose en los tribunales, pero en la noche electoral de 2024 ni siquiera hubo emoción. De hecho, prácticamente se repitió el mismo resultado final que en 2016 y 2020, aunque con distinto color. Trump recuperará el poder con casi el mismo número de electores que obtuvo en 2106, según las proyecciones: 311 ahora por 306 hace ocho años. Lo curioso es que 306 fue la misma cifra que dio la presidencia a Joe Biden en 2020, cuando desbancó a Trump. </p>



          </div>


          <div class="lv_mapBlock">


            <div class="lv_mapTitle">
              <!-- <h3>Elecciones EE.UU</h3> -->
              <h4>Resultado de las elecciones en EE.UU. desde 2000</h4>
              <p class="lv_mapExpl">Desliza la barra para consultar los resultados de cada año</p>

              <div class="lv_leyenda">

                 <div class="lv_leyendaG lv_leyenda1">
                  <span style="background-color: blue;"></span>
                  <p>P. Demócrata</p>
                 </div>



                 <div class="lv_leyendaG lv_leyenda2">
                  <span style="background-color: red;"></span>
                  <p>P. Republicano</p>
                 </div>



                 <div class="lv_leyendaG lv_leyenda3">
                  <span style="background-color: grey;"></span>
                  <p>Recuento en marcha</p>
                 </div>

              </div>
            </div>


            <div class="lv_controls">
              <input
                class="custom-range"
                type="range"
                min="0"
                max="100"
                step="1"
                bind:value={rangeValue}
                on:input={() => updateMapSourceByRange(rangeValue)}
              />

              <div class="lv_years-labels">
                {#each years as year, index}
                  <span class="lv_year-label" style="left: {index * (100 / (years.length - 1))}%">{year}</span>
                {/each}
              </div>
            </div>

            <div id="lv_map-container" class="lv_map"></div>
          </div>    <!-- END of map block -->



          <div class="lv_textContent">

            <div class="lv_textBlock">
  
  
  
              <p class="lv_Par">Esta repetición de resultados viene a confirmar la importancia de los estados bisagra. Reciben esta denominación no sólo aquellos con tendencia a cambiar con cierta frecuencia del rojo republicano al azul demócrata o al revés, como se observa en el gráfico, sino también los que se deciden por una diferencia de menos de tres puntos en el recuento y que, por lo tanto, son más susceptibles a poder cambiar de color. En contraste, los dos grandes partidos cuentan con bastiones inexpugnables donde es casi imposible que puedan perder una elección presidencial. 
              </p>


              <p class="lv_Par">Los republicanos cuentan con 13 estados bastiones como Texas, Alask, Kansas, Utah o Carolina del Sur, mientras que los demócratas siempre tienen la certeza de poder contar con los electores de 7 estados como Nueva York, Massachussets, Oregon o Hawai, más el distrito central capitalino de Washington.</p>
              <div class="lv_separator"></div>

              <div class="lv_destacado">
                <h5>El triunfo más amplio de este siglo lo obtuvo Barack Obama en el 2008, con 365 electores por 173 de John McCain</h5>
              </div>

              <div class="lv_separator"></div>


              <p class="lv_Par">Los estados bisagra, siete en esta contienda de 2024, son casi siempre los mismos, aunque a lo largo de la historia han ido cambiando. Por ejemplo, Ohio, que durante décadas era considerado el termómetro político de Estados Unidos, está ya fuera de la lista porque se ha convertido en un estado inclinado hacia los republicanos que ya no refleja la igualdad ideológica del país. Se decía antes que quien ganaba en Ohio, pero esa tendencia se rompió en 2020.  </p>


              <p class="lv_Par">Lo sorprendente es que Donald Trump haya ganado o esté liderando el recuento en los siete estados clave de estas elecciones: Carolina del Norte, Georgia, Wisconsin, Michigan, Pensilvania, Arizona y Nevada. Han sido triunfos inesperados porque las encuestas repartían más las preferencias entre los dos candidatos, pero en el fondo lo que ha hecho Trump es recuperar el territorio que le arrebató Joe Biden en 2020, y además incorporar Nevada, que había sido demócrata con Barack Obama, Hillary Clinton y Joe Biden.</p>


              <p class="lv_Par">El actual presidente, que renunció a presentarse a la reelección ante las encuestas negativas por su incapacidad para convencer de que era apto para seguir en el cargo pese a su edad, ganó en 2020 en seis de los siete estados bisagra, todos menos Carolina del Norte, que retuvo Trump en su fallido intento de reelección. Biden se benefició hace cuatro años del voto favorable en su estado natal, la decisiva Pensilvania, y aseguró Georgia por apenas unos 11.000 votos.</p>


              <p class="lv_Par">La victoria de Donald Trump queda lejos de las palizas históricas de Richard Nixon -520 a 17 frente a George McGovern- y del récord de Ronald Regan, cuando en 1984 logró la reelección con un 525 a 13 ante Walter Mondale, el candidato demócrata que solo logró vencer en Minnesota y el Washington D.C. El triunfo más amplio de este siglo lo obtuvo Barack Obama en el 2008, con 365 electores por 173 de John McCain.
              </p>


              


            </div>

          </div>












      </div>  <!-- /lv_article -->



    </div>  <!-- /lv_pieceWrapper -->




  </div>

</main>











<style>


:root {
    --slider-color: #a9bcd0;
    --caratula-texto:  #010d23f2;
   
  }



  .lv_mapBlock{
    margin-top: 40px;
  }




  /* CARATULA */

  .lv_caratula{
    width: 100vw;
    height: 100dvh;
    position: relative;

  }




  .lv_caratulaImg{
    position: absolute;
    top: 0;
    left:0;
    width: 100%;
    height: 100%;
    background-image: url('../src/assets/img/presMov.png');
    background-size: cover;
    background-position: center; 
    /* filter: grayscale(0%) opacity(80%) blur(2px); */
    display: flex;
    justify-content: center;
    align-items: center;

  }




  .lv_caratulaTexts{
    width: 100%;
    height: 100%;
    margin:0 auto;
    display: flex;
    flex-direction: column;
    justify-content: flex-end;
    align-items: center;
    padding: 20px;
    text-align: center;
    position: relative;
    z-index: 2;


  }


  .lv_caratulaTextsWrapper{
    width: 100%;
    max-width: 750px;
    padding: 20px;
    background-color: var(--caratula-texto);
    border-radius: 6px;
    color: #fff;

  }



  .lv_caratulaTexts h1{
    font-family: 'HBold', serif;
    font-size: 2.2rem;
    font-weight: 400;
    margin-bottom: 20px;
    margin-top: 20px;
    
  }


  .lv_separator {
    width: 40%;
    max-width: 300px;
    height: 2px;
    background-color: #cecece; 
    margin: 20px auto; 
    border-radius: 5px; 
    display: none;
  }



  .lv_caratulaTexts h4{
    font-family: 'TRegular', serif;
    font-size: 1.1rem;
    margin-bottom: 20px;
    display: none;
    
  }








  /* ARTICLE */





  /* Bloque autores */

  #lv_Authors{
    
    padding-top: 20px;
    margin-bottom: 80px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

  }

  .lv_authorsWrapper{

  }

  .lv_authorName{
    text-align: center;
  }

  .lv_authorName a{
      text-decoration: none;
      
  }

  .lv_authorName h4{
      text-transform: uppercase;
      color: #000;
      font-family: 'HMedium', serif;
      font-size: 16px;

  }

  .lv_authorName h5{
      margin: 12px 0;
      text-transform: uppercase;
      color: #000;
      font-family: 'HMedium', serif;
      font-size: 12px;

  }

  .lv_xarxesWrapper{
    display: flex;
    justify-content: center;
    margin-top: 10px;
  }

  .lv_xarxesWrapper .btn{
      width: 40px;
      margin-left: 6px;
      color: #fff;
      background-color: #bcbcbc;
      border-color: #bcbcbc;
      border: 1px solid transparent;
      padding: .375rem .75rem;
      font-size: 1rem;
      line-height: 1.5;
      border-radius: .25rem;
      border-radius: 50%

  }


  /* End of authors */









  /* MAPA */


  /* TITLE */

  .lv_mapTitle{
    width: 80%;
    max-width: 550px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding:30px 12px;
    text-align: center;
   /*  background: linear-gradient(180deg, rgba(0,28,76,1) 0%, rgba(169,188,208,1) 100%);
    color:#fff;
    border-radius: 7px; */

  }


  .lv_mapTitle h3{
    text-transform: uppercase;
    margin-bottom: 20px;
    font-family: 'HBold',serif;
    font-size: 1.5em;

  }





  .lv_mapTitle h4{
    font-family: 'HSemibold',serif;
    font-size: 1.5em;
    margin-bottom: 20px;

  }

  .lv_mapTitle p.lv_mapExpl{
    text-align: center;
    font-family: 'TRegular',serif;
    font-style: italic;
   

  }




  /* Leyenda */

  .lv_leyenda{
    margin-top: 22px;
    display: grid;
    grid-template-columns: 1fr 1fr;
    grid-template-rows: 1fr 1fr;
    background-color: #e2e2e2;
    border-radius: 4px;
    padding: 10px;
  }


  .lv_leyendaG{
    display:flex;
    justify-content: center;
    align-items: center;
    padding: 7px;

  }


  .lv_leyendaG span{
    display: inline-block;
    width: 10px;
    height: 10px;
  }


  .lv_leyendaG p{
    margin-left: 6px;
    font-family: 'TRegular', serif;
    font-size: .7rem;
    text-transform: uppercase;
    color:#7e7e7e;
    font-style: italic;

  }




  .lv_leyenda1{
    grid-column: 1 / span 1;
    grid-row: 1 / span 1;
  }

  .lv_leyenda2{
    grid-column: 2 / span 1;
    grid-row: 1 / span 1;
  }

  .lv_leyenda3{
    grid-column: 1 / span 2;
    grid-row: 2 / span 2;
    margin: 0 auto;
  }










  /* SLIDER */

  .lv_controls {
    margin-bottom: 20px;
    position: relative;
    width: 80%;
    margin: 0 auto;
    padding-top: 60px;
    padding-bottom: 60px;
  }


  .custom-range {
    -webkit-appearance: none;
    width: 100%;
    height: 20px; 
    background: var(--slider-color);
    border-radius: 10px;
    outline: none;
    position: relative;
    overflow: hidden;
  }

  .custom-range::-webkit-slider-runnable-track {
    width: 100%;
    height: 20px;
    background: var(--slider-color);
  }

  .custom-range::-webkit-slider-thumb {
    -webkit-appearance: none;
    appearance: none;
    width: 20px;
    height: 20px;
    background-color: rgba(0, 0, 0, 0.5);
    border-radius: 50%;
    cursor: pointer;
    position: relative;
    z-index: 1;
  }

  .custom-range::-moz-range-thumb {
    width: 20px;
    height: 20px;
    background-color: rgba(0, 0, 0, 0.5);
    border-radius: 50%;
    cursor: pointer;
  }

 

  /* AÑOS */


  .lv_years-labels {
    display: flex;
    justify-content: space-between;
    width: 100%;
    position: absolute;
    top: 30px; 
  }

  .lv_year-label {
    font-size: 0.8rem;
    color: #333;
    text-align: center;
    position: absolute;
    transform: translateX(-50%);
    font-family: 'HSemibold', serif;
  }

  .lv_map {
    width: 100%;
    height: 75dvh;
  }










  /* TEXTO ART */

  .lv_textBlock{
    margin-top: 60px;
  }



  .lv_Par{
    padding-bottom: 40px;
    padding-left: 15px;
    padding-right: 15px;
    max-width: 850px;
    margin: 0 auto;
    font-family: 'TRegular', serif;
    -webkit-font-smoothing: antialiased;
    font-size: 18px;
    line-height: 27px;
    font-weight: 400;
    letter-spacing: -.1px;
    word-wrap: break-word;
    
  }

  .lv_destacado{


  }



  .lv_destacado h5{
    padding: 20px 15px;
    max-width: 750px;
    margin: 0 auto;
    font-family: 'HSemibold', serif;
    font-size: 1.7rem;
    
  }




  /* MEDIA QUERIES */


  @media (min-width: 550px) {


    .lv_caratulaImg{
      background-image: url('../src/assets/img/presidentes.jpg');

    }

    .lv_caratulaTexts h1{
      font-family: 'HBold', serif;
      font-size: 2.9rem;
      margin-bottom: 40px;
      
    }


    .lv_caratulaTexts h4{
      font-family: 'TRegular', serif;
      font-size: 1.5rem;
      margin-bottom: 40px;
      
    }



    .lv_year-label {
      font-size: 0.9rem;

    }



    .lv_mapTitle{
      padding:50px 12px;

    }





  }




  @media (min-width: 750px) {

    .lv_leyenda{
      margin-top: 22px;
      display: flex;
      background-color: transparent;
      border-radius: 4px;
      padding: 10px;
    }


    .lv_destacado h5{
      font-size: 2rem;
      
    }

      .lv_separator {
      display: block;
    }



    .lv_caratulaTexts h4{
      display: block;
      
    }



  }





 
</style>
