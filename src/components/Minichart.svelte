<script>
  import * as d3 from "d3";
  import { fade } from "svelte/transition";
  import MiniLine from "./MiniLine.svelte";
  import Axes from "./Axes.svelte";
  import Defs from "./Defs.svelte";
  export let city;
  export let h;
  export let dateScale;
  export let domain;
  export let height;
  export let data;
  export let type;
  export let width;

  $: valueScale = d3
    .scaleLinear()
    .domain(domain)
    .range([h * 0.95, h * 0.2]);

  var dashCode = {
    2019: "3, 3",
    2020: "2, 2",
    2021: "1.5, 1.5",
    2022: "0",
  };

  var colorCode = new Map();

  colorCode.set("Mannheim", {
    2019: "#F88379",
    2020: "#FF3131",
    2021: "#A52A2A",
    2022: "#630330",
  });

  colorCode.set("Stuttgart", {
    2019: "#ADD8E6",
    2020: "#7FFFD4",
    2021: "#00BFFF",
    2022: "#00008B",
  });

  colorCode.set("Berlin", {
    2019: "#8FBC8F",
    2020: "#9ACD32",
    2021: "#228B22",
    2022: "#006400",
  });

  colorCode.set("München", {
    2019: "#ff7f50",
    2020: "#f08080",
    2021: "#ff6347",
    2022: "#8B4726",
  });

  function createTicks(d) {
    var diff = d[1] - d[0];
    var step = diff / 10;
    return range(d[0], d[1] + step, step);
  }

  function range(start, stop, step) {
    if (typeof stop == "undefined") {
      // one param defined
      stop = start;
      start = 0;
    }

    if (typeof step == "undefined") {
      step = 1;
    }

    if ((step > 0 && start >= stop) || (step < 0 && start <= stop)) {
      return [];
    }

    var result = [];
    for (var i = start; step > 0 ? i < stop : i > stop; i += step) {
      result.push(i);
    }

    return result;
  }
</script>

<svg xmlns="http://www.w3.org/2000/svg" {width} {height}>
  <Defs />
  <Axes
    {dateScale}
    {valueScale}
    mini={true}
    years={Array.from(data.keys())}
    {dashCode}
    {city}
    {width}
    dateTicks={createTicks([1546300800, 1577836799])}
    valueTicks={createTicks(domain)}
    {type}
    show={data.size > 0}
  />
  {#each Array.from(data.keys()) as year}
    <MiniLine
      {dateScale}
      {valueScale}
      colorCode={colorCode.get(city)}
      {dashCode}
      {year}
      data={data.get(year)}
    />
  {/each}
</svg>
