<template>
  <div class="treemap">
    <!-- The SVG structure is explicitly defined in the template with attributes derived from component data -->
    <svg :height="height" style="margin-left: 0px;" :width="width">
      <g style="shape-rendering: crispEdges;" transform="translate(0,20)">
        <!-- We can use Vue transitions too! -->
        <transition-group name="list" tag="g" class="depth">
          <!-- Generate each of the visible squares at a given zoom level (the current selected node) -->
          <g
            class="children"
            v-for="(children, index) in selectedNode._children"
            :key="'c_' + children.id"
            v-if="selectedNode"
          >
            <!-- Generate the children squares (only visible on hover of a square) -->
            <rect
              v-for="child in children._children"
              class="child"
              :id="child.id"
              :key="'d_' + child.id"
              :height="y(child.y1) - y(child.y0)"
              :width="x(child.x1) - x(child.x0)"
              :x="x(child.x0)"
              :y="y(child.y0)"
            ></rect>

            <!--
              The visible square rect element.
              You can attribute directly an event, that fires a method that changes the current node,
              restructuring the data tree, that reactivly gets reflected in the template.
            -->
            <rect
              class="parent"
              v-on:click="selectNode"
              :id="children.id"
              :key="'a_' + children.id"
              :x="x(children.x0)"
              :y="y(children.y0)"
              :width="x(children.x1 - children.x0 + children.parent.x0)"
              :height="y(children.y1 - children.y0 + children.parent.y0)"
              :style="{ fill: color(index) }"
            >
              <!-- The title attribute -->
              <title>{{ children.data.name }} | {{ children.value }}</title>
            </rect>

            <!-- The visible square text element with the title and value of the child node -->
            <text
              dy="1em"
              :key="'t_' + children.id"
              :x="x(children.x0) + 6"
              :y="y(children.y0) + 6"
              style="fill-opacity: 1;"
            >{{ children.data.name }}</text>

            <text
              dy="2.25em"
              :key="'x_' + children.id"
              :x="x(children.x0) + 6"
              :y="y(children.y0) + 6"
              style="fill-opacity: 1;"
            >{{ children.value }}</text>
          </g>
        </transition-group>

        <!-- The top most element, representing the previous node -->
        <g class="grandparent">
          <rect
            :height="margin.top"
            :width="width"
            :y="(margin.top * -1)"
            v-on:click="selectNode"
            :id="parentId"
          ></rect>

          <!-- The visible square text element with the id (basically a breadcumb, if you will) -->
          <text dy=".65em" x="6" y="-14">{{ selectedNode.id }}</text>
        </g>
      </g>
    </svg>
  </div>
</template>

<script>
import { scaleLinear, scaleOrdinal } from 'd3-scale';
import { json } from 'd3-request';
import { hierarchy, treemap } from 'd3-hierarchy';
import { schemeCategory10 } from 'd3-scale-chromatic';

// To be explicit about which methods are from D3 let's wrap them around an object
// Is there a better way to do this?
const d3 = {
  scaleLinear,
  scaleOrdinal,
  schemeCategory10,
  json,
  hierarchy,
  treemap,
};

export default {
  name: 'Treemap',
  // the component's data
  data() {
    return {
      jsonData: {
        name: 'Topics',
        children: [
          {
            name: 'Business',
            children: [
              {
                name: 'Company',
                children: [
                  { name: 'Date Company Was Founded', value: 10 },
                  { name: 'Revenue Trend', value: 20 },
                  { name: 'Cost Trend', value: 10 },
                  { name: 'Valuation', value: 30 },
                  { name: 'Key Man Risks', value: 10 },
                  { name: 'Owner Absenteeism', value: 10 },
                ],
              },
            ],
          },
          {
            name: 'Traffic',
            children: [
              {
                name: 'Traffic Trend',
                children: [
                  { name: 'Last 3m vs Prev 3m', value: 33.3 },
                  { name: 'Last 6m vs Prev 6m', value: 33.3 },
                  { name: 'Last 12m vs Prev 12m', value: 33.3 },
                ],
              },
              {
                name: 'Traffic Diversification',
                children: [
                  { name: 'Organic', value: 14.286 },
                  { name: 'Direct', value: 14.286 },
                  { name: 'Referral', value: 14.286 },
                  { name: 'Social', value: 14.286 },
                  { name: 'Email', value: 14.286 },
                  { name: 'Paid Search', value: 14.286 },
                  { name: 'News Aggregators', value: 14.286 },
                ],
              },
              {
                name: 'Traffic Value',
                children: [
                  { name: 'Ahrefs Traffic Value', value: 70 },
                  {
                    name: '% of traffic in English speaking countries',
                    value: 30,
                  },
                ],
              },
              {
                name: 'Landing Page Diversity',
                children: [
                  {
                    name: '% of organic traffic to top landing page',
                    value: 70,
                  },
                  { name: 'Other', value: 30 },
                ],
              },
            ],
          },
          {
            name: 'Money',
            children: [
              {
                name: 'Revenue Stream Diversity',
                children: [
                  { name: 'Revenue Stream #1 Earnings', value: 25 },
                  { name: 'Revenue Stream #2 Earnings', value: 25 },
                  { name: 'Revenue Stream #3 Earnings', value: 25 },
                  { name: 'Revenue Stream #4 Earnings', value: 25 },
                ],
              },
            ],
          },
          {
            name: 'Website',
            children: [
              {
                name: 'Backlink Health',
                children: [
                  { name: 'Anchor Text Diversity %', value: 25 },
                  { name: 'Topical Relevance Check', value: 10 },
                  { name: 'Paid Links Check', value: 25 },
                  { name: 'Sitewide Links Check', value: 10 },
                  { name: "What's the Link Velocity Like?", value: 30 },
                ],
              },
              {
                name: 'Content Health',
                children: [
                  { name: '% Duplicate Content', value: 15 },
                  { name: 'Average Word Count', value: 25 },
                  { name: 'Citing Image Sources?', value: 0 },
                  { name: 'Check Content Quality', value: 60 },
                ],
              },
              {
                name: 'Social Media',
                children: [
                  {
                    name: 'Social Media Score',
                    children: [
                      { name: 'Facebook Followers', value: 20 },
                      { name: 'Twitter Followers', value: 5 },
                      { name: 'Youtube Followers', value: 30 },
                      { name: 'Instagram Followers', value: 15 },
                      { name: 'Linkedin Followers', value: 25 },
                      { name: 'Pinterest Followers', value: 5 },
                    ],
                  },
                  {
                    name: 'Email List',
                    children: [
                      { name: 'Active Openers', value: 50 },
                      { name: 'Frequency of Emails', value: 20 },
                      { name: 'Growth', value: 30 },
                    ],
                  },
                ],
              },
            ],
          },
        ],
      },
      rootNode: {},
      margin: {
        top: 20,
        right: 0,
        bottom: 0,
        left: 0,
      },
      width: 960,
      height: 530,
      selected: null,
      color: {},
    };
  },
  // You can do whatever when the selected node changes
  watch: {
    selectedNode(newData, oldData) {
      console.log('The selected node changed...');
    },
  },
  // In the beginning...
  mounted() {
    const that = this;

    // An array with colors (can probably be replaced by a vuejs method)
    console.log('d3.schemeCategory10 :', d3);
    that.color = d3.scaleOrdinal(d3.schemeCategory10);
    that.initialize();
    that.accumulate(that.rootNode, that);
    that.treemap(that.rootNode);
    // // loads the data and calls the initialization methods
    // d3.json('../static/flare.json', (error, data) => {
    //   if (error) console.log(error);

    // });
  },
  // The reactive computed variables that fire rerenders
  computed: {
    // The grandparent id
    parentId() {
      if (
        this.selectedNode.parent === undefined
        || this.selectedNode.parent === null
      ) {
        return this.selectedNode.id;
      }
      return this.selectedNode.parent.id;
    },
    // Returns the x position within the current domain
    // Maybe it can be replaced by a vuejs method
    x() {
      return d3
        .scaleLinear()
        .domain([0, this.width])
        .range([0, this.width]);
    },
    // Returns the y position within the current domain
    // Maybe it can be replaced by a vuejs method
    y() {
      return d3
        .scaleLinear()
        .domain([0, this.height - this.margin.top - this.margin.bottom])
        .range([0, this.height - this.margin.top - this.margin.bottom]);
    },
    // The D3 function that recursively subdivides an area into rectangles
    treemap() {
      return d3
        .treemap()
        .size([this.width, this.height])
        .round(false)
        .paddingInner(0);
    },
    // The current selected node
    selectedNode() {
      let node = null;

      if (this.selected) {
        const nd = this.getNodeById(this.rootNode, this.selected, this);

        if (nd._children) {
          node = nd;
        } else {
          node = nd.parent;
        }
      } else {
        node = this.rootNode;
      }

      // Recalculates the y and x domains
      this.x.domain([node.x0, node.x0 + (node.x1 - node.x0)]);
      this.y.domain([node.y0, node.y0 + (node.y1 - node.y0)]);

      return node;
    },
  },
  methods: {
    // Called once, to create the hierarchical data representation
    initialize() {
      const that = this;

      if (that.jsonData) {
        that.rootNode = d3
          .hierarchy(that.jsonData)
          .eachBefore((d) => {
            d.id = (d.parent ? `${d.parent.id}.` : '') + d.data.name;
          })
          .sum(d => d.value)
          .sort((a, b) => b.height - a.height || b.value - a.value);
        that.rootNode.x = 0;
        that.rootNode.y = 0;
        that.rootNode.x1 = that.width;
        that.rootNode.y1 = that.height;
        that.rootNode.depth = 0;
      }
    },
    // Calculates the accumulated value (sum of children values) of a node - its weight,
    // represented afterwards in the area of its square
    accumulate(d, context) {
      d._children = d.children;

      if (d._children) {
        d.value = d._children.reduce(
          (p, v) => p + context.accumulate(v, context),
          0,
        );
        return d.value;
      }
      return d.value;
    },
    // Helper method - gets a node by its id attribute
    getNodeById(node, id, context) {
      if (node.id === id) {
        return node;
      }
      if (node._children) {
        for (let i = 0; i < node._children.length; i++) {
          const nd = context.getNodeById(node._children[i], id, context);
          if (nd) {
            return nd;
          }
        }
      }
    },
    // When a user clicks a square, changes the selected data attribute
    // which fires the computed selectedNode, which in turn finds the Node by the id of the square clicked
    // and the template reflects the changes
    selectNode(event) {
      this.selected = event.target.id;
    },
  },
};
</script>

<style scoped>
text {
  pointer-events: none;
}

.grandparent text {
  font-weight: bold;
}

rect {
  fill: none;
  stroke: #fff;
}

rect.parent,
.grandparent rect {
  stroke-width: 2px;
}

.grandparent rect {
  fill: orange;
}

.grandparent:hover rect {
  fill: #ee9700;
}

.children rect.parent,
.grandparent rect {
  cursor: pointer;
}

.children rect.parent {
  fill: #bbb;
  fill-opacity: 0.5;
}

.children:hover rect.child {
  fill: #bbb;
}

.children text {
  font-size: 0.8em;
}

.grandparent text {
  font-size: 0.9em;
}

.list-enter-active,
.list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to /* .list-leave-active for <2.1.8 */ {
  opacity: 0;
}
</style>
