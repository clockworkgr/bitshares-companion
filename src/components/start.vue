<template>
    <div class="bottom">
        <div class="content">
            <p
                v-if="!hasWallet"
                class="mt-3 mb-3 font-weight-normal"
            >
                <em>{{ $t('no_wallet') }}</em>
            </p>
            <router-link
                v-if="!hasWallet"
                to="/create"
                tag="button"
                class="btn btn-lg btn-primary btn-block"
                replace
            >
                {{ $t('start_cta') }}
            </router-link>
            <p
                v-if="!hasWallet"
                class="my-2 font-weight-normal"
            >
                <em>{{ $t('or') }}</em>
            </p>
            <router-link
                v-if="!hasWallet"
                to="/restore"
                tag="button"
                class="btn btn-lg btn-secondary btn-block"
                replace
            >
                {{ $t('restore_cta') }}
            </router-link>

            <span
                v-if="hasWallet"
                class="icon-account"
            />
            <multiselect
                v-if="hasWallet"
                id="wallet-select"                
                v-model="selectedWallet"
                :class="'form-control my-3 accountProvide text-left'"
                :searchable="false"
                :placeholder="$t('select_wallet')"
                label="name"
                :allow-empty="false"
                :options="walletlist"        
                track-by="id"
                @change="passincorrect=''"
            />
            <br>
            <span
                v-if="hasWallet"
                class="icon-lock1"
            />
            <input
                v-if="hasWallet"
                id="inputPassword"
                v-model="walletpass"
                type="password"
                class="form-control mb-4 px-3"
                :placeholder=" $t('password_placeholder')"
                required
                
                :class="passincorrect"
                @focus="passincorrect=''"
            >
            <button
                v-if="hasWallet"
                class="btn btn-lg btn-primary btn-block"
                type="submit"
                @click="unlockWallet"
            >
                {{ $t('unlock_cta') }}
            </button>
            <p
                v-if="hasWallet"
                class="my-2 font-weight-normal"
            >
                <em>{{ $t('or') }}</em>
            </p>
            <div class="row">
                <div class="col-6">
                    <router-link
                        v-if="hasWallet"
                        to="/create"
                        tag="button"
                        class="btn btn-lg btn-primary btn-block"
                        replace
                    >
                        {{ $t('create_cta') }}
                    </router-link>
                </div>
                <div class="col-6">
                    <router-link
                        v-if="hasWallet"
                        to="/restore"
                        tag="button"
                        class="btn btn-lg btn-secondary btn-block"
                        replace
                    >
                        {{ $t('restore_cta') }}
                    </router-link>
                </div>
            </div>
        </div>
        <b-modal
            id="error"
            ref="errorModal"
            centered
            hide-footer
            title="Error"
        >
            {{ errorMsg }}
        </b-modal>
        <p class="mt-2 mb-2 small">
            &copy; 2019 BitShares
        </p>
    </div>
</template>
<script>
    import RendererLogger from "../lib/RendererLogger";
    import Multiselect from "vue-multiselect";
    const logger = new RendererLogger();

    export default {
        name: "Start",
        components: { Multiselect },
        i18nOptions: { namespaces: "common" },
        data() {
            return {
                walletpass: "",
                selectedWallet: null,
                passincorrect: "",
                errorMsg: ""
            };
        },
        computed: {
            hasWallet() {
                return this.$store.state.WalletStore.hasWallet;
            },
            walletlist() {
                return this.$store.state.WalletStore.walletlist;
            } 
        },
        mounted() {
            logger.debug("Start screen mounted");
            this.$store.dispatch("WalletStore/loadWallets", {}).catch(() => {});
        },
        methods: {
            unlockWallet() {
                this.$store
                    .dispatch("WalletStore/getWallet", {
                        wallet_id: this.selectedWallet.id,
                        wallet_pass: this.walletpass
                    })
                    .then(() => {
                        this.$router.replace("/dashboard");
                    })
                    .catch(() => {
                        this.passincorrect = "is-invalid";
                        this.errorMsg = "Invalid Password.";
                        this.$refs.errorModal.show();
                    });
            }
        }
    };
</script>