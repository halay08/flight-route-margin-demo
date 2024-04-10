# Flight Route Margin demo

This repository is a demonstration showcasing the creation of margins for flight routes. It utilizes CesiumJS and TurfJS libraries.

Due to the particular operating form and operating area of agricultural spraying UAVs, users generally divide the operating area on the ground station map according to the boundary of the ditches. However, there is usually a gap between the ditch and the actual spraying area. If the boundary of the ditches is directly used as the operating area, it will lead to a waste of pesticides. Therefore, the margins of the operating area need to be reduced to avoid pesticide waste.

![Margin Solution](./img/margin-solution.png)

In this repository, we do the opposite. We use the actual spraying area to generate the margin polygon.

## Getting Started

1. For each edge of polygon, we create a margin line.

```js
const margin = turf.lineOffset(edge, 1000, { units: 'meters' })
```

2. For each line, we extend the start/end points by distance of `MARGIN * 20`. We need to make the offset line longer to find the intersection point.

```js
// Calculate point C, MARGIN * 20 meters extended from A in the opposite direction
pointC = turf.destination(pointC, MARGIN * 20, bearingCD - 180, { units: 'meters' })

// Calculate point D, MARGIN * 20 meters extended from B in the same direction
pointD = turf.destination(pointD, MARGIN * 20, bearingCD, { units: 'meters' })

const line = turf.lineString([
  [pointC.geometry.coordinates[0], pointC.geometry.coordinates[1]],
  [pointD.geometry.coordinates[0], pointD.geometry.coordinates[1]],
])
```

3. Find the intersections of each offset lines, then we have the vertices of the margin polygon.

```js
const intersects = turf.lineIntersect(line, nextLine)
```

4. Regenerate the flight route based on the margin polygon.

## Development

```sh
$ npm i -g http-server
http-server
```

View on browser http://127.0.0.1:8080

![Demo Map](./img/demo-map.png)
