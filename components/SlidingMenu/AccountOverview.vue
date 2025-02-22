<template>
  <div class="account-overview">
    <div class="account-overview__user">
      <nuxt-link
        :to="{ 'name': 'in' }"
      >
        <div @click="closeSlidingMenu">
          <lc-avatar
            :src="getUserInfo.avatar"
            :halo="avatarHalo"
          />
        </div>
      </nuxt-link>
      <div class="account-overview__id lc-font-size-18 lc-font-weight-600">
        <span class="lc-color-gray-9b">ID:</span>
        <nuxt-link
          :to="{ 'name': 'in' }"
        >
          <span
            class="lc-color-like-green lc-line-height-1-2"
            @click="closeSlidingMenu"
          >
            {{ getUserInfo.user }}
          </span>
        </nuxt-link>
      </div>
    </div>

    <div
      v-if="usdStrValue && (getUserHasWallet || getUserHasERC20Wallet)"
      class="account-overview__account lc-padding-bottom-32"
    >
      <div class="account-overview__like-amount">
        <span class="lc-font-size-38 lc-font-weight-300 lc-color-like-gray-5">
          {{ likeCoinStrValue }}
        </span><span class="account-overview__like-currency">LIKE</span>
      </div>
      <div class="lc-font-weight-600 lc-color-gray-9b">
        (USD {{ usdStrValue }})
      </div>
    </div>
    <div
      v-else
      class="account-overview__account lc-padding-bottom-48"
    />

    <div class="account-overview__cta-wrapper lc-text-align-center">
      <div @click="closeSlidingMenu">
        <md-button
          :to="{ 'name': 'in-civic' }"
          class="md-likecoin lc-secondary lc-font-size-18 lc-font-weight-600"
        >{{ $t('CivicPage.title') }}</md-button>
      </div>
      <a
        :href="PURCHASE_LIKE_URL"
        class="lc-underline lc-margin-top-12"
        rel="noopener noreferrer"
        target="_blank"
      >{{ $t('Home.Sale.button.tradeAtExchange') }}</a>
    </div>
  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex';

import { PURCHASE_LIKE_URL } from '@/constant';

import User from '@/util/User';

export default {
  name: 'account-overview',
  data() {
    return {
      PURCHASE_LIKE_URL,
    };
  },
  computed: {
    ...mapGetters([
      'getUserIsRegistered',
      'getUserInfo',
      'getUserHasWallet',
      'getUserHasERC20Wallet',
      'getLikeCoinUsdNumericPrice',
      'getUserLikeCoinAmountInBigNumber',
    ]),
    avatarHalo() {
      return User.getAvatarHaloType(this.getUserInfo);
    },
    likeCoinStrValue() {
      return this.getUserLikeCoinAmountInBigNumber
        ? this.getUserLikeCoinAmountInBigNumber.toFixed(2) : '';
    },
    usdStrValue() {
      if (this.getLikeCoinUsdNumericPrice && this.getUserLikeCoinAmountInBigNumber) {
        return (
          this.getUserLikeCoinAmountInBigNumber.times(this.getLikeCoinUsdNumericPrice).toFixed(2)
        );
      }
      return null;
    },
  },
  mounted() {
    if (this.getUserIsRegistered) {
      this.queryLikeCoinUsdPrice();
      this.queryLikeCoinWalletBalance();
    }
  },
  methods: {
    ...mapActions([
      'closeSlidingMenu',
      'queryLikeCoinUsdPrice',
      'queryLikeCoinWalletBalance',
    ]),
  },
};
</script>


<style lang="scss" scoped>
@import "~assets/variables";
.account-overview {
  position: relative;

  background-image: linear-gradient(221deg, #d2f0f0, #f0e6b4);

  &__user {
    display: flex;
    align-items: center;
    flex-direction: row;

    padding-top: 16px;
    padding-left: 32px;

    img {
      width: 56px;
      height: 56px;

      border-radius: 50%;
      background-color: $like-white;

      object-fit: cover;
    }
  }

  &__id {
    display: flex;
    align-items: center;
    flex-direction: row;

    padding-left: 8px;

    span:last-child {
      display: inline-block;
      overflow: hidden;

      padding-left: 4px;

      white-space: nowrap;
      text-overflow: ellipsis;

      @media (min-width: 600px + 1px) {
        width: 200px;
      }
      @media (max-width: 600px) {
        width: 136px;
      }
    }
  }

  &__account {
    @media (min-width: 600px + 1px) {
      margin-top: -10px;
      padding-left: 96px;
    }
    @media (max-width: 600px) {
      padding-left: 32px;
    }
  }

  &__like-amount {
    display: flex;
    align-items: center;
    flex-direction: row;
    flex-wrap: wrap;
  }

  &__like-currency {
    padding-left: 16px;

    color: $like-dark-brown-2;
  }

  .md-likecoin {
    align-self: center;

    min-width: 188px;
    margin: 0;

    box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.5);

    /deep/ .md-ripple {
      min-height: 36px;
    }
  }

  &__cta-wrapper {
    position: absolute;
    bottom: -50px;

    display: flex;
    flex-direction: column;

    width: 100%;
  }
}
</style>
