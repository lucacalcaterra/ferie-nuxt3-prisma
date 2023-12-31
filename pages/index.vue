<script lang="ts" setup>
import { UserI } from "../types";
import { Ref, ComputedRef } from "vue";
import useAuth from "~/composables/useAuth";
// import { useCasl } from "~/composables/useCasl";
import { CaslActionE, CaslSubjectE } from "../types";
const { t } = useI18n();

useHead({
  title: t("welcome"),
});
const { useAuthUser, initAuth, useAuthLoading, logout } = useAuth();
const router = useRouter();
const loading = ref(false);
const isAuthLoading = useAuthLoading();
let isAuthenticated: ComputedRef<boolean> = computed(
  () => !!(user.value && user.value.username)
);
const { can, cannot } = useCasl();
initAuth();

const user: Ref<UserI> = useAuthUser();
onBeforeMount(async () => {
  initAuth();
  isAuthenticated = computed(() => !!(user.value && user.value.username));

  loading.value = true;
  if (user) {
    try {
      loading.value = true;
    } catch (error) {
      console.log(error);
    } finally {
      loading.value = false;
    }
  }
});
</script>
<template>
  <ClientOnly>
    <NuxtLayout name="default">
      <MainSection class="fit no-borders" title="Home" :loading="loading">
        <LoadingPage v-if="isAuthLoading" />

        <!-- App -->
        <div clas="all-pages q-px-md">
          <section class="lg:px-[15%] px-[5%] pt-20">
            <h3 class="lg:text-5xl text-2xl leading-normal font-semibold text-center revrainbow">
              Check Out Our Latest
            </h3>
          </section>
          <h4>
            <a target="_blank" href="https://casl.js.org/v6/en">CASL Documentaions</a>
          </h4>
          <fieldset>
            <legend>{{ $t("demoContent.q1") }}</legend>
            <q-btn
              v-if="can(CaslActionE.READ, CaslSubjectE.POST)"
              label="Create Post"
              color="positive"
            ></q-btn>
            <pre lang="html">
              v-if="can('create', 'Post')"                         
            </pre>
          </fieldset>
          <fieldset>
            <legend>{{ $t("demoContent.q2") }}</legend>
            <p v-if="can(CaslActionE.READ, CaslSubjectE.POST)">POST content ...</p>
            <pre lang="html">
              v-if="can('read', 'Post')"                         
            </pre>
          </fieldset>
          <fieldset>
            <legend>{{ $t("demoContent.q3") }}</legend>
            <q-btn
              label="Update Post"
              color="warning"
              :disable="can(CaslActionE.UPDATE, CaslSubjectE.POST)"
            ></q-btn>
            <pre lang="html">
              :disable="can('update', 'Post')"                         
            </pre>
          </fieldset>

          <fieldset>
            <legend>{{ $t("demoContent.q4") }}</legend>
            <q-btn
              label="Delete Post"
              color="negative"
              :disable="cannot(CaslActionE.DELETE, CaslSubjectE.POST)"
            ></q-btn>
            <pre lang="javascript">
              :disable="cannot('delete', 'Post')"                         
            </pre>
          </fieldset>

          <slot />
        </div>
        <div class="q-px-md">
          <q-btn
            class="q-my-md q-ml-none`"
            :color="isAuthenticated ? 'green' : 'red'"
            text="white"
            :label="$t(isAuthenticated ? 'navigation.Signout' : 'navigation.Signin')"
            @click.prevent="isAuthenticated ? logout() : router.push('/auth/login')"
          />
        </div>
      </MainSection>
    </NuxtLayout>
  </ClientOnly>
</template>
<style>
pre {
  width: 100%;
  white-space: pre-wrap; /* css-3 */
  white-space: -moz-pre-wrap; /* Mozilla, since 1999 */
  white-space: -pre-wrap; /* Opera 4-6 */
  white-space: -o-pre-wrap; /* Opera 7 */
  word-wrap: break-word; /* Internet Explorer 5.5+ */
}
</style>
