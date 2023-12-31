<template>
  <div>
    <app-container>
      <template #container>
        <app-header />

        <app-content :class="contentCls">
          <template #content>
            <Flex :class="searchCls">
              <Flex
                :class="[showFilters ? 'leftbar' : 'leftbar-optional']"
                :style="{ flex: '20%', background: '#0C0E12' }"
                flexDirection="column"
              >
                <app-section>
                  <selected-filters
                    className="filter-class"
                    filterLabel="Filters"
                    :showClearAll="true"
                    clearAllLabel="Clear filters"
                  />
                </app-section>
                <app-section>
                  <app-title>Release Year</app-title>
                  <range-input
                    componentId="year-list"
                    dataField="release_year"
                    filterLabel="Release Year"
                    :range="{
                      start: 1990,
                      end: 2021,
                    }"
                    :rangeLabels="{
                      start: '1990',
                      end: '2021',
                    }"
                    :defaultValue="{
                      start: 1990,
                      end: 2021,
                    }"
                    className="range-slider-input"
                    :showHistogram="false"
                    :stepValue="1"
                    :react="{
                      and: [
                        'SearchSensor',
                        'language-list',
                        'results',
                        'price',
                      ],
                    }"
                    :innerClass="{
                      slider: 'range-slider',
                    }"
                  />
                </app-section>
                <app-section>
                  <app-title>Genres</app-title>
                  <multi-list
                    componentId="language-list"
                    className="year-filter"
                    dataField="genres_new_data.keyword"
                    :size="100"
                    sortBy="count"
                    queryFormat="or"
                    selectAllLabel="All Genres"
                    :showCheckbox="true"
                    :showSearch="true"
                    :innerClass="{
                      label: 'multilist-checkbox',
                      checkbox: 'checkbox',
                      input: 'multilist-input',
                    }"
                    placeholder="Search for a genre"
                    :react="{
                      and: ['SearchSensor', 'results', 'price', 'year-list'],
                    }"
                    :showFilter="true"
                    filterLabel="Genre"
                    :URLParams="false"
                  >
                    <div
                      slot="renderItem"
                      slot-scope="{ label, count }"
                      :style="{ width: '100%' }"
                    >
                      <span class="lang-container">
                        <span class="multilist-checkbox">
                          {{ languageMap[label] || label }}
                        </span>
                        <span
                          v-if="count"
                          :style="{ color: 'rgb(240, 240, 240)' }"
                          >{{ count }}</span
                        >
                      </span>
                    </div>
                  </multi-list>
                </app-section>
                <app-section>
                  <!-- <app-title>Price</app-title> -->
                  <range-slider
                    componentId="price"
                    :react="{
                      and: ['SearchSensor', 'language-list', 'year-list'],
                    }"
                    dataField="price"
                    filterLabel="Price"
                    :innerClass="{
                      title: 'range-slider-title',
                      label: 'range-slider-label',
                      slider: 'range-slider',
                    }"
                    title="Price"
                    :range="{
                      start: 0,
                      end: 1500,
                    }"
                    :showHistogram="false"
                    :rangeLabels="{
                      start: '$0',
                      end: '$1500',
                    }"
                  />
                </app-section>
              </Flex>

              <Flex
                :style="{
                  flex: '80%',
                  background: themeConfig.secondaryBg,
                }"
                flexDirection="column"
                :class="[showFilters ? 'rightbar' : 'rightbar-optional']"
              >
                <reactive-list
                  componentId="results"
                  dataField="original_title"
                  :react="{
                    and: [
                      'SearchSensor',
                      'price',
                      'language-list',
                      'year-list',
                    ],
                  }"
                  :innerClass="{
                    list: 'search-results',
                    pagination: 'pagination',
                    poweredBy: 'powered-by',
                    sortOptions: 'sort-options',
                    resultStats: 'resultStats',
                  }"
                  :pagination="true"
                  paginationAt="bottom"
                  :pages="5"
                  :size="4"
                  Loader="Loading..."
                  noResults="No results were found..."
                  :sortOptions="[
                    {
                      dataField: '_score',
                      sortBy: 'desc',
                      label: 'Sort by Best Match \u00A0 \u00A0',
                    },
                    {
                      dataField: 'vote_average',
                      sortBy: 'desc',
                      label: 'Sort by Ratings(High to Low) \u00A0',
                    },
                    {
                      dataField: 'price',
                      sortBy: 'asc',
                      label: 'Sort by Price(Low to High) \u00A0',
                    },
                    {
                      dataField: 'original_title.keyword',
                      sortBy: 'asc',
                      label: 'Sort by Title(A-Z) \u00A0',
                    },
                  ]"
                >
                  <div slot="renderNoResults">
                    <Flex class="noResults">
                      <span>No results were found...</span>
                    </Flex>
                  </div>
                  <div
                    slot="renderItem"
                    slot-scope="{ item, triggerClickAnalytics }"
                  >
                    <product-card
                      :id="item.id"
                      :posterPath="item.poster_path"
                      :originalTitle="item.original_title"
                      :releaseYear="item.release_year"
                      :genresData="item.genres_data"
                      :overview="item.overview"
                      :price="item.price"
                      :voteAverage="item.vote_average"
                      :product="item"
                      @productClick="triggerClickAnalytics"
                    />
                  </div>
                </reactive-list>
              </Flex>

              <a-button class="filter-btn" @click="toggleFilters">
                <a-icon :type="displayFilter()" />
              </a-button>
            </Flex>
            <div :class="footerCls">
              Appbase.io ©{{ new Date().getFullYear() }} created by Appbase Inc.
            </div>
          </template>
        </app-content>
      </template>
    </app-container>
  </div>
</template>

<script>
import { css } from '@emotion/css';
import { styled } from '@egoist/vue-emotion';
import Container from '../components/Layout/Container.vue';
import Header from '../components/Layout/Header/index.vue';
import Content from '../components/Layout/Content.vue';
import ProductCard from '../components/Product/ProducrCard.vue';
import media from '../utils/media';
import { themeConfig } from '../utils/constants';
import { languageMap } from '../utils/helper';

export const Section = styled('div')`
  border-bottom: 0.5px solid #29303e;
  padding: 30px;
`;

export const Title = styled('h3')`
  padding: '10px 0';
  color: #fdfdfd;
  opacity: 0.65;
  font-family: Lato;
`;

const contentCls = css`
  height: 84vh;
  overflow-y: scroll;
  ${media.medium(css`
    margin-top: 112px;
  `)};
`;

const searchCls = css`
  display: flex;
  felx-direction: row;
  margin-bottom: 40px;
  width: 100%;
  ${media.medium(css`
    margin-bottom: 30px;
  `)};
  .filter-class {
    a {
      max-width: 230px;
      &:hover {
        color: ${themeConfig.secondary};
      }
    }
  }
  .filter-btn {
    background-color: ${themeConfig.secondary};
    z-index: 10;
    width: 40px;
    height: 40px;
    border-radius: 20px;
    position: fixed;
    top: 180px;
    right: 20px;
    color: #fff;
    font-size: 26px;
    padding: 2px 6px;
    cursor: pointer;
    display: none;
    &:hover {
      border: 1px solid #fff;
    }
  }
  .year-filter {
    color: #808184;
    font-size: 13px;
  }

  .year-filter > div > div {
    .vue-slider-component {
      .vue-slider {
      }
    }
  }
  .lang-container {
    width: 100%;
    display: flex;
    flex-direction: row;
    -webkit-box-pack: justify;
    justify-content: space-between;
    line-height: 1.3rem;
  }
  .multilist-checkbox {
    margin-right: 10px;
    &:before {
      border: 1px solid #808184;
      background-color: transparent;
    }
    &:after {
      border-color: ${themeConfig.secondary};
    }
  }
  .multilist-input {
    width: 100%;
    height: 42px;
    padding: 8px 12px;
    border: 1px solid rgb(102, 102, 102);
    font-size: 0.9rem;
    outline: none;
    background-color: rgb(33, 33, 33);
    color: rgb(151, 151, 151);

    &:focus {
      background-color: rgb(33, 33, 33);
    }
  }
  .checkbox {
    &:hover {
      + label {
        &:hover {
          color: ${themeConfig.secondary};
        }
        &::before {
          border-color: ${themeConfig.secondary};
        }
      }
    }
    &:checked {
      + label {
        &::before {
          border: 1px solid #808184;
          background-color: transparent;
        }
      }
    }
  }

  .vue-slider-dot-handle {
    background: #ff3957;
    border: none;
  }

  .vue-slider-tooltip {
    background: #ff3957;
    border: #ff3957;
  }

  .range-slider {
    color: #ff3957;
    .vue-slider-dot-handle {
      background-color: ${themeConfig.secondary};
      border: none;
    }
    .vue-slider-process {
      background-color: ${themeConfig.secondary};
      border-radius: 15px;
    }
    .label-container {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
  }

  .range-slider-input {
    .vue-slider-dot-handle {
      background-color: ${themeConfig.secondary};
      border: none;
    }
    .vue-slider-process {
      background-color: ${themeConfig.secondary};
      border-radius: 15px;
    }
    .label-container {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
  }

  .range-slider-label {
    font-family: Lato;
    font-size: 14px;
    line-height: 17px;
    color: #fdfdfd;
    opacity: 0.65;
  }
  .range-slider-title {
    font-size: 18.72px;
    color: #fdfdfd;
    opacity: 0.65;
    font-weight: 500;
  }
  .search-results {
    flex-direction: row;
    display: flex;
    flex-wrap: wrap;
    margin-top: 20px;
  }
  .noResults {
    width: 100%;
    align-items: center;
    justify-content: center;
    height: 500px;
    font-size: 20px;
    color: ${themeConfig.secondary};
  }
  .powered-by {
    display: none;
  }
  .pagination {
    a {
      background-color: #0c0e12;
      &:hover {
        color: ${themeConfig.secondary};
      }
      color: #fff;
    }
    a[disabled] {
      color: #3b3c3f;
    }
    .disabled {
      background-color: #0c0e12;
      color: #3b3c3f;
    }
    .active {
      background-color: ${themeConfig.secondary};
    }
  }
  .sort-options {
    margin-right: 30px;
  }
  .rightbar,
  .rightbar-optional {
    padding: 50px;
  }
  .resultStats {
    color: #fff;
    opacity: 0.95;
  }
  ${media.medium(css`
    .sort-options {
      margin-right: 0;
    }
    .rightbar,
    .rightbar-optional {
      padding: 20px;
    }
    .resultStats {
      display: none;
    }
    .filter-btn {
      display: block;
    }
    .leftbar-optional {
      display: none;
    }
    .rightbar {
      display: none;
    }
  `)}
`;

const footerCls = css`
  text-align: center;
  background: #04070b;
  color: #fff;
  padding: 24px 50px;
  font-size: 14px;
  ${media.medium(css`
    margin-bottom: 100px;
    padding: 17px 50px;
  `)};
`;

export default {
  components: {
    'app-container': Container,
    'app-content': Content,
    'app-header': Header,
    'product-card': ProductCard,
    'app-title': Title,
    'app-section': Section,
  },
  data() {
    return {
      showFilters: false,
      error: false,
      languageMap,
      themeConfig,
      searchCls,
      footerCls,
      contentCls,
    };
  },
  methods: {
    toggleFilters() {
      this.showFilters = !this.showFilters;
    },
    displayFilter() {
      if (this.showFilters) {
        return 'close';
      }
      return 'filter';
    },
  },
};
</script>
