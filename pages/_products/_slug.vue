<template>
  <div>
    <Loader :loading="isLoading" />
    <SfBreadcrumbs
      class="breadcrumbs desktop-only"
      :breadcrumbs="breadcrumbs"
    />
    <div v-if="!product" id="error">
      <SfImage
        class="image"
        :src="require('../../static/assets/not-found.svg')"
      />
      <SfHeading
        title="Page not found"
        subtitle="We are sorry that we can’t find the page, please go back or try again"
        class="heading sf-heading--no-underline"
      />
      <div class="actions">
        <SfButton
          class="sf-button--full-width actions__button"
          @click="window.location = '/'"
        >
          Return to home
        </SfButton>
        <SfButton
          class="sf-button--full-width sf-button--text actions__button"
          @click="$emit('click:back')"
        >
          Back
        </SfButton>
      </div>
    </div>
    <div v-if="product" id="product">
      <div class="product">
        <div class="product__gallery">
          <SfGallery
            class="gallery-mobile mobile-only"
            :image-width="375"
            :image-height="490"
            :images="product.imageList"
          />
          <div v-for="(image, index) in product.imageList" :key="index">
            <SfImage
              :src="image.desktop.url"
              :width="590"
              :height="700"
              class="desktop-only"
            />
          </div>
        </div>
        <div class="product__description">
          <SfSticky class="product-details">
            <div class="product-details__mobile-top">
              <div>
                <SfHeading
                  :title="product.name"
                  :level="1"
                  class="sf-heading--no-underline sf-heading--left product-details__heading"
                />
                <div class="product-details__sub">
                  <SfPrice
                    :regular="'$' + product.prices.price.value.toFixed(2)"
                    class="sf-price--big product-details__sub-price"
                  />
                  <div class="product-details__sub-rating">
                    <SfRating :score="product.reviewSummary.score" :max="5" />
                    <div class="product-details__sub-reviews desktop-only">
                      {{ product.reviewSummary.numberOfReviews }}
                      <span v-if="product.reviewSummary.numberOfReviews == 1">
                        Review
                      </span>
                      <span v-if="product.reviewSummary.numberOfReviews !== 1">
                        Reviews
                      </span>
                    </div>
                    <div class="product-details__sub-reviews mobile-only">
                      ({{ product.reviewSummary.numberOfReviews }})
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <p
              class="product-details__description desktop-only"
              :v-text="product.description"
            />
            <div class="product-details__section">
              <SfSelect
                v-for="(option, i) in product.options.edges"
                :key="i"
                v-model="optionSelections[option.node.entityId]"
                :label="option.node.displayName"
                class="sf-select--bordered product-details__attribute"
              >
                <SfSelectOption
                  v-for="(value, x) in option.node.values.edges"
                  :key="x"
                  :value="value.node.entityId"
                >
                  {{ value.node.label }}
                  <!-- <SfProductOption :label="value.node.label" /> -->
                </SfSelectOption>
              </SfSelect>
            </div>
            <div class="product-details__section">
              <SfAlert
                message="Low in stock"
                type="warning"
                class="product-details__alert mobile-only"
              />
              <SfAddToCart
                v-model="qty"
                :stock="stock"
                :can-add-to-cart="stock > 0"
                class="product-details__add-to-cart"
                @click="addCart"
              />
              <SfButton class="product-details__add-to-cart" @click="buyNow">
                Buy Now
              </SfButton>
            </div>
            <SfTabs
              class="product-details__tabs"
              :open-tab="product.description ? 1 : 2"
            >
              <SfTab
                v-if="
                  product.description.length > 0 ||
                  product.customFields.edges.length > 0
                "
                title="Description"
              >
                <div>
                  <!-- eslint-disable-next-line vue/no-v-html -->
                  <p v-html="product.description" />
                </div>
                <div class="product-details__properties">
                  <SfProperty
                    v-for="(customField, i) in product.customFields.edges"
                    :key="i"
                    :name="customField.node.name"
                    :value="customField.node.value"
                    class="product-property"
                  />
                </div>
              </SfTab>
              <SfTab v-if="product.brand" title="Additional Information">
                <SfHeading
                  :title="product.brand.name"
                  :level="3"
                  class="sf-heading--no-underline sf-heading--left"
                />
                <p v-text="product.brand.metaDesc" />
              </SfTab>
            </SfTabs>
          </SfSticky>
        </div>
      </div>
      <SfBanner
        title="Download our application to your mobile"
        image="/assets/storybook/Home/bannerD.png"
        class="banner-application sf-banner--left sf-banner--center desktop-only"
      >
        <template #subtitle>
          <div class="banner-application__subtitle">Fashion to Take Away</div>
        </template>
        <template #title>
          <h1 class="banner-application__title">
            Download our application to your&nbsp;mobile
          </h1>
        </template>
        <template #call-to-action>
          <div>
            <img
              class="banner-application__download"
              src="/assets/storybook/Home/google.png"
              alt=""
            />
            <img
              class="banner-application__download"
              src="/assets/storybook/Home/apple.png"
              alt=""
            />
          </div>
        </template>
      </SfBanner>
    </div>
  </div>
</template>
<script>
import {
  SfProperty,
  SfHeading,
  SfPrice,
  SfRating,
  SfSelect,
  // SfProductOption,
  SfAddToCart,
  SfTabs,
  SfGallery,
  SfImage,
  SfBanner,
  SfAlert,
  SfSticky,
  SfBreadcrumbs,
  SfButton
} from '@storefront-ui/vue';
import { mapGetters } from 'vuex';
import Loader from '~/components/Loader.vue';
export default {
  name: 'Product',
  components: {
    SfAlert,
    SfProperty,
    SfHeading,
    SfPrice,
    SfRating,
    SfSelect,
    // SfProductOption,
    SfAddToCart,
    SfTabs,
    SfGallery,
    SfImage,
    SfBanner,
    SfSticky,
    SfBreadcrumbs,
    SfButton,
    Loader
  },
  async asyncData({ params, $queries, $axios }) {
    const result = await $axios.$post('/graphql', {
      query: $queries.productBySlug(params)
    });
    const productData = result.data?.site?.route?.node;
    const optionSelections = {};

    if (productData != null) {
      productData.imageList = productData.images.edges.map((t) => {
        return {
          mobile: { url: t.node.mobile },
          desktop: { url: t.node.desktop },
          big: { url: t.node.big }
        };
      });

      productData.options.edges.forEach((o) => {
        optionSelections[o.node.entityId] = null;
      });

      productData.reviewSummary.score =
        productData.reviewSummary.summationOfRatings /
        productData.reviewSummary.numberOfRatings;
    }

    return { product: productData, optionSelections };
  },
  data() {
    const productData = {
      product: {},
      optionSelections: {},
      qty: '1',
      stock: 1,
      breadcrumbs: [
        {
          text: 'Shop All',
          link: '/shop-all'
        },
        {
          text: 'Product Page',
          link: '#'
        }
      ]
    };
    return productData;
  },
  computed: {
    ...mapGetters('carts', ['isLoading'])
  },
  methods: {
    addCart() {
      const variants = this.product.variants.edges;
      const addData = {
        quantity: this.qty,
        product_id: this.product.entityId
      };
      if (variants.length > 0) addData.variant_id = variants[0].node.entityId;
      this.$store.dispatch('carts/addToCart', addData);
    },
    buyNow(event) {
      window.location = this.product.addToCartUrl.replace('add', 'buy');
    }
  }
};
</script>
<style src="~/assets/sass/pages/product.scss" lang="scss" scoped></style>
