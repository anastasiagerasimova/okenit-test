<template>
    <div ref="map-root"
        style="width: 100%; height: 100%"
    >
        <div id="popup">
            <p>{{coordinate[0]}}</p>
            <p>{{coordinate[1]}}</p>
            <div class="popup__line"></div>
            <button class="popup__btn" v-on:click="deleteMarker"><i class="fa fa-times" aria-hidden="true"></i> Удалить</button>
        </div>
    </div>
</template>

<script>
    import {transform} from 'ol/proj'
    import View from 'ol/View'
    import Map from 'ol/Map'
    import TileLayer from 'ol/layer/Tile'
    import OSM from 'ol/source/OSM'
    import VectorLayer from 'ol/layer/Vector'
    import VectorSource from 'ol/source/Vector'
    import Feature from "ol/Feature"
    import Point from "ol/geom/Point";
    import GeoJSON from 'ol/format/GeoJSON'
    import {Circle as CircleStyle, Fill, Stroke, Style, RegularShape} from 'ol/style';
    import Overlay from 'ol/Overlay';
    import Select from 'ol/interaction/Select';
    import {defaults as defaultInteractions,} from 'ol/interaction';

    import 'ol/ol.css'

    const styles = {
        'square': new Style({
            image: new RegularShape({
            fill: new Fill({color: 'red'}),
            stroke: new Stroke({color: 'black', width: 1}),
            points: 4,
            radius: 8,
            angle: Math.PI / 4,
            }),
        }),
        'circle': new Style({
            image: new CircleStyle({
                radius: 6,
                fill: new Fill({color: 'red'}),
                stroke: new Stroke({
                    color: [0,0,0], width: 1
                })
            })
        }),
        'triangle': new Style({
            image: new RegularShape({
            fill: new Fill({color: 'red'}),
            stroke: new Stroke({color: 'black', width: 1}),
            points: 3,
            radius: 8,
            angle: 0,
            }),
        })
    }

    export default {
        name: 'MapContainer',
        data(){
            return{
                map: null,
                source: null,
                featureID: 0,
                selectedFeatureID: null,
                selectInteraction: null,
                coordinate: [],
                popup: null,
                data: {
                    type: "FeatureCollection",
                    features: []
                }
            }
        },
        components: {},
        props: ['markerType'],
        mounted() {
            this.createMap()
            this.map.on('click', this.onMapClick)

            this.map.on('pointermove', (e) => {
                if (this.map.hasFeatureAtPixel(e.pixel) && !styles[this.markerType]){
                    this.map.getViewport().style.cursor = 'pointer'
                }else {
                    this.map.getViewport().style.cursor = 'inherit'
                }
            })

            this.selectInteraction.on('select', (e) =>{
                if(e.selected[0]){
                    this.selectedFeatureID = e.selected[0].getId(); 
                    this.onMarkerClick(e.selected[0])   
                }
            });

        },
        methods: {
            onMapClick(event){
                if(styles[this.markerType]){
                    this.popup.getElement().style.display = 'none'
                    this.map.removeInteraction(this.selectInteraction)

                    const featureToAdd = new Feature({
                        geometry: new Point(event.coordinate)
                    })
                    featureToAdd.setStyle(styles[this.markerType])
                    featureToAdd.setId(this.featureID+=1)
                    this.source.addFeature(featureToAdd)

                }else{

                    if (this.map.hasFeatureAtPixel(event.pixel)) {
                        this.map.addInteraction(this.selectInteraction)
                    }else {
                        this.popup.getElement().style.display = 'none'
                    }

                }
            },
            deleteMarker(){
                const featureToDelete = this.source.getFeatureById(this.selectedFeatureID)
                this.source.removeFeature(featureToDelete)
            },
            onMarkerClick(selectedFeature){  
                const coordinate = selectedFeature.getGeometry().getCoordinates()
                this.coordinate = transform(coordinate, 'EPSG:3857', 'EPSG:4326')
                this.popup.setPosition(coordinate);
                this.popup.getElement().style.display = 'block'
            },
            createMap(){
                const feature = new GeoJSON({
                    featureProjection: 'EPSG:3857'
                }).readFeatures(this.data)

                this.source = new VectorSource({
                    features: [...feature],
                })

                this.popup = new Overlay({
                    element: document.getElementById('popup'),
                    positioning: 'bottom-center',
                    stopEvent: false,
                    offset: [0, -15],
                });
                this.popup.getElement().style.display = 'none'

                this.selectInteraction = new Select();

                this.map  = new Map({
                    target: this.$refs['map-root'],
                    layers: [
                        new TileLayer({source: new OSM()}),
                        new VectorLayer({source: this.source})
                    ],
                    view: new View({
                        zoom: 0,
                        center: [0, 0],
                        constrainResolution: true
                    }),
                    overlays: [this.popup],
                    interactions: defaultInteractions().extend([this.selectInteraction])
                })
            }
        }
    }
</script>

<style scoped>
    #popup{
        font-size: 10px;
        width: auto;
        height: auto;
        background: rgba(255,255,255,.85);
        border-radius: 2px;
        padding: 3px 6px 4px;
    }
    #popup p{
        margin: 0;
        padding: 0;
        margin-bottom: 5px;
    }

    .popup__line{
        height: 1px;
        background: #dadada;
        margin-bottom: 5px;
    }
    .popup__btn{
        padding: 3px;
        background: transparent;
        border: none;
        outline: none;
        font-size: 13px;
        cursor: pointer;
        color: red;
    }
</style> 