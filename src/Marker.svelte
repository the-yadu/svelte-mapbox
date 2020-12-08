<script>
  import { onMount,createEventDispatcher } from 'svelte'
  import { getContext } from 'svelte'
  import { contextKey } from './mapbox.js'
  
  const dispatch = createEventDispatcher();

  const { getMap, getMapbox } = getContext(contextKey)
  const map = getMap()
  const mapbox = getMapbox()

  function randomColour () {
    return Math.round(Math.random() * 255)
  }

  function move (lng, lat) {
    marker.setLngLat({ lng, lat })
  }

  export let lat
  export let lng
  export let label = 'Marker'
  export let popupClassName = 'beyonk-mapbox-popup'
  export let color = randomColour()
  export let popup = true
  export let draggable = false

  let marker

  $: marker && move(lng, lat)

  onMount(() => {
    marker = new mapbox.Marker({ color,draggable })

    if (popup) {
      new mapbox.Popup({
        offset: 25,
        className: popupClassName
      })
        .setText(label)

      marker.setPopup(popup)
    }
    marker.on('dragend', onDragEnd);
    marker
      .setLngLat({ lng, lat })
      .addTo(map)
    
    marker.on("dragend", () => {
      dispatch("dragend", marker.getLngLat());
    });

    return () => marker.remove()
  })

  export function getMarker () {
    return marker
  }
</script>
