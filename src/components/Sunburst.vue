<template>
  <div>
    <sunburst class="sunburst" :data="tree">
      <!-- Add behaviors -->
      <template slot-scope="{ nodes, actions }">
        <highlightOnHover :nodes="nodes" :actions="actions"/>
        <zoomOnClick :nodes="nodes" :actions="actions"/>
      </template>

      <!-- Add bottom legend -->
      <breadcrumbTrail
        slot="legend"
        slot-scope="{ nodes, colorGetter, width }"
        :current="nodes.mouseOver"
        :root="nodes.root"
        :colorGetter="colorGetter"
        :from="nodes.clicked"
        :width="width"
      />
      <!-- Add information to be displayed on top the graph -->
      <nodeInfoDisplayer
        class="info"
        slot="top"
        slot-scope="{ nodes }"
        :current="nodes.mouseOver"
        :root="nodes.root"
        description="value"
      />
    </sunburst>
  </div>
</template>

<script>
import {
  breadcrumbTrail,
  highlightOnHover,
  nodeInfoDisplayer,
  sunburst,
  zoomOnClick,
} from 'vue-d3-sunburst';
import 'vue-d3-sunburst/dist/vue-d3-sunburst.css';

export default {
  components: {
    breadcrumbTrail,
    highlightOnHover,
    nodeInfoDisplayer,
    sunburst,
    zoomOnClick,
  },
  data() {
    return {
      tree: {
        name: 'Topics',
        children: [
          {
            name: 'Business',
            children: [
              {
                name: 'Company',
                children: [
                  { name: 'Date Company Was Founded', size: 10 },
                  { name: 'Revenue Trend', size: 20 },
                  { name: 'Cost Trend', size: 10 },
                  { name: 'Valuation', size: 30 },
                  { name: 'Key Man Risks', size: 10 },
                  { name: 'Owner Absenteeism', size: 10 },
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
                  { name: 'Last 3m vs Prev 3m', size: 33.3 },
                  { name: 'Last 6m vs Prev 6m', size: 33.3 },
                  { name: 'Last 12m vs Prev 12m', size: 33.3 },
                ],
              },
              {
                name: 'Traffic Diversification',
                children: [
                  { name: 'Organic', size: 14.286 },
                  { name: 'Direct', size: 14.286 },
                  { name: 'Referral', size: 14.286 },
                  { name: 'Social', size: 14.286 },
                  { name: 'Email', size: 14.286 },
                  { name: 'Paid Search', size: 14.286 },
                  { name: 'News Aggregators', size: 14.286 },
                ],
              },
              {
                name: 'Traffic Value',
                children: [
                  { name: 'Ahrefs Traffic Value', size: 70 },
                  {
                    name: '% of traffic in English speaking countries',
                    size: 30,
                  },
                ],
              },
              {
                name: 'Landing Page Diversity',
                children: [
                  {
                    name: '% of organic traffic to top landing page',
                    size: 70,
                  },
                  { name: 'Other', size: 30 },
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
                  { name: 'Revenue Stream #1 Earnings', size: 25 },
                  { name: 'Revenue Stream #2 Earnings', size: 25 },
                  { name: 'Revenue Stream #3 Earnings', size: 25 },
                  { name: 'Revenue Stream #4 Earnings', size: 25 },
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
                  { name: 'Anchor Text Diversity %', size: 25 },
                  { name: 'Topical Relevance Check', size: 10 },
                  { name: 'Paid Links Check', size: 25 },
                  { name: 'Sitewide Links Check', size: 10 },
                  { name: "What's the Link Velocity Like?", size: 30 },
                ],
              },
              {
                name: 'Content Health',
                children: [
                  { name: '% Duplicate Content', size: 15 },
                  { name: 'Average Word Count', size: 25 },
                  { name: 'Citing Image Sources?', size: 0 },
                  { name: 'Check Content Quality', size: 60 },
                ],
              },
              {
                name: 'Social Media',
                children: [
                  {
                    name: 'Social Media Score',
                    children: [
                      { name: 'Facebook Followers', size: 20 },
                      { name: 'Twitter Followers', size: 5 },
                      { name: 'Youtube Followers', size: 30 },
                      { name: 'Instagram Followers', size: 15 },
                      { name: 'Linkedin Followers', size: 25 },
                      { name: 'Pinterest Followers', size: 5 },
                    ],
                  },
                  {
                    name: 'Email List',
                    children: [
                      { name: 'Active Openers', size: 50 },
                      { name: 'Frequency of Emails', size: 20 },
                      { name: 'Growth', size: 30 },
                    ],
                  },
                ],
              },
            ],
          },
        ],
      },
    };
  },
};
</script>

<style scoped>
.sunburst {
  margin: auto;
  width: 500px;
  height: 500px;
  position: relative;
}
.infornation-sunburst {
  left: 65%;
}
</style>
