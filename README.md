# 2023-ol-workshop

## Presentation:
    https://slides.com/nihelg/openlayers-101

### Getting started
```ts
const map = new Map({ // create Map object
  target: 'map', // points to <div> in .html
  layers: [ // define list of layers
    new TileLayer({ // type of layer
      source: new OSM() // source of layer
    })
  ],
  view: new View({ // customize map object
    center: fromLonLat([-93.41, 44.92]), // define center point
    zoom: 9 // zoom level
  })
});
 ```   
### Change the Basemap
```ts
 import XYZ from 'ol/source/XYZ' // import new module. Tile data arranged by standard XYZ grid. Accessed through URLs

 const map = new Map({
   target: 'map',
   view: new View({
     center: fromLonLat([-93.41, 44.92]),
     zoom: 9,
   }),
   layers: [
     new TileLayer({
       source: new XYZ({ // new source module that fetches url
         url: 'https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}',
         maxZoom: 19
       })
     }),
   ],
 });
```

### Scaleline
```ts
import ScaleLine from 'ol/control/ScaleLine.js';
const scaleLineControl = new ScaleLine();
map.addControl(scaleLineControl);
```

### Add some data
```ts
 import VectorSource from 'ol/source/Vector';
 import { Vector as VectorLayer } from 'ol/layer';
 import { GeoJSON } from 'ol/format';

 const trails = new VectorLayer({ // Type of layer
   source: new VectorSource({ // how to fetch it
     url: '/trails.json', // path to data (Metro Council Trails)
     format: new GeoJSON() // required for rendering since fetching data from URL
   }),
 });

 const map = new Map({
   target: 'map',
   layers: [
     new TileLayer({
       source: new OSM()
     }),
     trails // New map layer
   ],
   view: new View({
     center: fromLonLat([-93.41, 44.92]),
     zoom: 9,
   })
 });
```
### Simple Style
```ts
     import { Stroke, Style } from 'ol/style';
     
     const trails = new VectorLayer({ // Type of layer
       source: new VectorSource({ // how to fetch it
         url: '/trails.json', // path to data (Metro Council Trails)
         format: new GeoJSON() // required for rendering since fetching data from URL
       }),
       style: new Style({ // new Style constructor
         stroke: new Stroke({ // Line type, so need Stroke symbolizer
           color: 'green',
           width: 3,
         })
       }),
     });
 ```    

### Attribute based styles

```ts
function style(feature: Feature<any>) {
    const website = feature.getProperties().website;

     const blue = new Fill({
        color: 'rgb(56, 154, 234)',
      });
     const gray = new Fill({
        color: 'rgb(158, 158, 158)',
      });
      const stroke = new Stroke({
        color: 'white',
        width: 1.25,
      });

    if(website){
      return new Style({
          // blue color
          image: new Circle({
            fill: blue,
            stroke: stroke,
            radius: 5,
          }),
          fill: blue,
          stroke: stroke,
      });
    } 

    return new Style({
        // gray color
        image: new Circle({
          fill: gray,
          stroke: stroke,
          radius: 5,
        }),
        fill: gray,
        stroke: stroke,
    });
}
```
     
### Overlays
```ts
    import Overlay from 'ol/Overlay';

    // Elements that make up the pop-up
    const container = document.getElementById('popup');
    const content = document.getElementById('popup-content');

    // Create a new one to anchor it to the map
    const overlay = new Overlay({
      element: container,
    });

    const trails = new VectorLayer({
      source: new VectorSource({
        url: '/trails.json',
        format: new GeoJSON()
      }),
      style: new Style({
        stroke: new Stroke({
          color: 'green',
          width: 5,
        })
      }),
    });

    const map = new Map({
      target: 'map',
      layers: [
        new TileLayer({
          source: new OSM()
        }),
        trails
      ],
      overlays: [overlay], // Adds overlay to map
      view: new View({
        center: fromLonLat([-93.41, 44.92]),
        zoom: 9,
      })
    });

    map.on('click', (event) => { // Create Click interaction with map
      const trailheads = map.getFeaturesAtPixel(event.pixel);
      const coordinate = event.coordinate;

      if (trailheads.length) {
        content.innerHTML = `<p>${trailheads[0].get('NAME')}</p>`
        overlay.setPosition(coordinate)
      }
    });
    
    
    /* Styling for Popup */
    .ol-popup {
      position: absolute;
      background-color: white;
      box-shadow: 0 1px 4px rgba(0,0,0,0.2);
      padding: 15px;
      border-radius: 10px;
      border: 1px solid #cccccc;
      bottom: 12px;
      left: -50px;
      min-width: 200px;
    }
    .ol-popup:after, .ol-popup:before {
      top: 100%;
      border: solid transparent;
      content: " ";
      height: 0;
      width: 0;
      position: absolute;
      pointer-events: none;
    }
    .ol-popup:after {
      border-top-color: white;
      border-width: 10px;
      left: 48px;
      margin-left: -10px;
    }
    .ol-popup:before {
      border-top-color: #cccccc;
      border-width: 11px;
      left: 48px;
      margin-left: -11px;
    }
    
        <!-- Popup HTML -->
    <div id="popup" class="ol-popup">
      <div id="popup-content"></div>
    </div>
   ```  
     
