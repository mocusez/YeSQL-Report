<template>
    <Vega :spec="spec" />
</template>
<script setup lang="ts">
import Vega from './Vega.vue'
const spec = {
"$schema": "https://vega.github.io/schema/vega-lite/v5.json",
"description": "Library speed",
"width": 700,
"height": 350,
"data": {
    "values": [
    {"library": "postgres.plpython", "time": 68},
    {"library": "spark.pyspark", "time": 45},
    {"library": "dbX.python", "time": 38},
    {"library": "yesql.sqlite.cpython", "time": 14},
    {"library": "pandas", "time": 10},
    {"library": "monetdb.numpy", "time": 8},
    {"library": "tuplex", "time": 6},
    {"library": "yesql.sqlite.pypy", "time": 5},
    {"library": "yesql.monetdb.cpython", "time": 3},
    {"library": "yesql.monetdb.pypy", "time": 1}
    ]
},
"transform": [{"calculate": "datum.time + 'x'", "as": "time_with_x"}],
"encoding": {
    "y": {
    "field": "library",
    "type": "nominal",
    "sort": "-x",
    "axis": {"title": null}
    },
    "x": {
    "field": "time",
    "type": "quantitative",
    "scale": {"domain": [0, 70]},
    "axis": {"title": "time(sec)"}
    }
},
"layer": [
    {"mark": "bar"},
    {
    "mark": {"type": "text", "align": "left", "baseline": "middle", "dx": 3},
    "encoding": {"text": {"field": "time_with_x", "type": "nominal"}}
    }
],
"config": {}
} 

</script>
