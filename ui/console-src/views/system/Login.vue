<script lang="ts" setup>
import { computed } from "vue";
import LoginForm from "@/components/login/LoginForm.vue";
import { useRouteQuery } from "@vueuse/router";
import SignupForm from "@/components/signup/SignupForm.vue";
import SocialAuthProviders from "@/components/login/SocialAuthProviders.vue";
import { useGlobalInfoFetch } from "@console/composables/use-global-info";
import { useTitle } from "@vueuse/core";
import { useI18n } from "vue-i18n";
import { AppName } from "@/constants/app";
import MdiKeyboardBackspace from "~icons/mdi/keyboard-backspace";
import LocaleChange from "@/components/common/LocaleChange.vue";

const { globalInfo } = useGlobalInfoFetch();
const { t } = useI18n();

const SIGNUP_TYPE = "signup";

function onLoginSucceed() {
  window.location.reload();
}

function onSignupSucceed() {
  window.location.href = "/uc";
}

const type = useRouteQuery<string>("type", "");

function handleChangeType() {
  type.value = type.value === SIGNUP_TYPE ? "" : SIGNUP_TYPE;
}

const isLoginType = computed(() => type.value !== SIGNUP_TYPE);

useTitle(
  computed(() => {
    const siteTitle = globalInfo.value?.siteTitle || AppName;
    const routeTitle = t(
      `core.${type.value === SIGNUP_TYPE ? SIGNUP_TYPE : "login"}.title`
    );
    return [routeTitle, siteTitle].join(" - ");
  })
);
</script>
<template>
  <div class="flex w-72 flex-col">
    <SignupForm v-if="type === 'signup'" @succeed="onSignupSucceed" />
    <LoginForm v-else @succeed="onLoginSucceed" />
    <SocialAuthProviders />
    <div class="flex justify-center gap-2 pt-3.5 text-xs">
      <div v-if="globalInfo?.allowRegistration" class="space-x-0.5">
        <span class="text-slate-500">
          {{
            isLoginType
              ? $t("core.login.operations.signup.label")
              : $t("core.login.operations.return_login.label")
          }},
        </span>
        <span
          class="cursor-pointer text-secondary hover:text-gray-600"
          @click="handleChangeType"
        >
          {{
            isLoginType
              ? $t("core.login.operations.signup.button")
              : $t("core.login.operations.return_login.button")
          }}
        </span>
      </div>
      <RouterLink
        :to="{ name: 'ResetPassword' }"
        class="text-secondary hover:text-gray-600"
      >
        {{ $t("core.login.operations.reset_password.button") }}
      </RouterLink>
    </div>
    <div class="flex justify-center pt-3.5">
      <a
        class="inline-flex items-center gap-0.5 text-xs text-gray-600 hover:text-gray-900"
        href="/"
      >
        <MdiKeyboardBackspace class="!h-3.5 !w-3.5" />
        <span> {{ $t("core.login.operations.return_site") }} </span>
      </a>
    </div>
  </div>
  <div
    class="bottom-0 mb-10 mt-auto flex items-center justify-center gap-2.5 pt-3.5"
  >
    <LocaleChange />
  </div>
</template>
