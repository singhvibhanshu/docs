<template>
    <div id="nav-toc-global" class="bd-toc d-lg-flex justify-content-end">
        <div>
            <template v-if="generated.length > 0" class="bd-contents-list">
                <button
                    class="btn d-lg-none"
                    :class="tableOfContentsExpanded = !tableOfContentsExpanded ? '' : 'collapsed'"
                    type="button"
                    data-bs-toggle="collapse"
                    data-bs-target="#tocContents"
                    :aria-expanded="tableOfContentsExpanded"
                    aria-controls="tocContents"
                    @click="tableOfContentsExpanded = !tableOfContentsExpanded"
                >
                    Table of Contents
                    <ChevronUp v-if="tableOfContentsExpanded"/>
                    <ChevronDown v-else/>
                </button>

                <div class="collapse bd-toc-collapse" id="tocContents">
                    <slot name="header"></slot>
                    <strong class="d-none d-lg-block h6 mb-2">Table of Contents</strong>
                    <nav id="nav-toc">
                        <ul class="ps-0 pt-2 pt-lg-0 mb-2" v-for="tableOfContent in generated">
                            <li v-if="tableOfContent.depth > 1 && tableOfContent.depth < 6 && tableOfContent.text"
                                @click="closeToc" class="table-content">
                                <a @click="menuNavigate" :href="`#${tableOfContent.id}`" :class="`depth-${tableOfContent.depth}`">{{ tableOfContent.text }}</a>
                            </li>
                            <ul class="ps-0 pt-2 pt-lg-0"
                                v-if="tableOfContent.children && tableOfContent.children.length">
                                <template v-for="item in tableOfContent.children">
                                    <li v-if="item.depth > 1 && item.depth < 6" @click="closeToc"
                                        :class="{'mt-3': item.depth === 2}">
                                        <a @click="menuNavigate" :href="`#${item.id}`"
                                           :class="`depth-${item.depth}`">{{ item.text }}</a>
                                    </li>
                                </template>
                            </ul>
                        </ul>
                    </nav>
                </div>
            </template>

            <div class="d-none d-lg-block pt-4 bd-social-list">
                <CommonSocialsList :page="page" />
            </div>
        </div>
    </div>
</template>

<script setup>
    import ChevronUp from "vue-material-design-icons/ChevronUp.vue";
    import ChevronDown from "vue-material-design-icons/ChevronDown.vue";
</script>

<script>
    export default {
        props: {
            page: {
                type: Object,
                required: true
            },
            capitalize: {
                type: Boolean,
                default: false
            }
        },
        data() {
            return {
                tableOfContentsExpanded: false,
                showThankYou: false
            }
        },
        computed: {
            generated() {
                return this.page.body?.toc?.links ?? [];
            }
        },
        mounted() {
            this.scrollToHash();
            window.addEventListener('scroll', this.handleScroll);
        },
        beforeDestroy() {
            window.removeEventListener('scroll', this.handleScroll);
        },
        methods: {
            closeToc() {
                this.tableOfContentsExpanded = false;
                document.getElementById('tocContents').classList.remove("show");
            },
            handleScroll() {
              if (window.scrollY === 0) {
                this.removeActiveTab();
              } else {
                this.generated.forEach((link, i) => {
                  this.activateMenuItem(link, i, this.generated, this.removeActiveTab);
                  if (link.children) {
                    link.children.forEach((childrenLink, index) => {
                      this.activateMenuItem(childrenLink, index, link.children, this.removeActiveTab);
                    })
                  }
                });
              }
            },
            removeActiveTab() {
              document.querySelectorAll('.depth-2').forEach((item) => {
                item.classList.remove('active');
              })
              document.querySelectorAll('.depth-3').forEach((item) => {
                item.classList.remove('active');
              })
            },
            menuNavigate(e) {
              this.scrollIntoView(e.target.name);
              window.location.hash = e.target.name;
              setTimeout(() => {
                this.removeActiveTab();
                e.target.classList.add('active');
              }, 1000);
            },
            activateMenuItem(item, index, linkArray, removeActiveTab) {
              if (item?.id) {
                const childrenLinkPosition = document.querySelector(`#${item.id}`)?.getBoundingClientRect();
                const prevChildrenLinkPosition = index > 0 ? document.querySelector(`#${linkArray[index - 1].id}`)?.getBoundingClientRect()?.top : undefined;
                if (childrenLinkPosition?.top <= 160) {
                  let activeTapItem = document.querySelector(`.right-menu a[href='#${item.id}']`);
                  if (!activeTapItem.classList.contains('active')) {
                    if ((prevChildrenLinkPosition <= 0 || prevChildrenLinkPosition === undefined)) {
                      removeActiveTab();
                      activeTapItem.classList.add('active');
                    }
                  }
                }
              }
            },
            scrollToHash() {
              const hash = this.$route.hash;
              if (hash) {
                const targetId = hash.substring(1);
                this.scrollIntoView(targetId);
              }
            },
            scrollIntoView(id) {
              const element = document.getElementById(id);
              this.$nextTick(() => {
                if (element) {
                  setTimeout(() => {
                    element.scrollIntoView({block: "nearest", inline: "nearest"});
                  }, 100);
                } else {
                  setTimeout(() => {
                    this.$nextTick(() => {
                      const updatedElement = document.getElementById(id);
                      if (updatedElement) {
                        updatedElement.scrollIntoView({block: "nearest", inline: "nearest"});
                      }
                    });
                  }, 1000);
                }
              });
            }
        },
        watch: {
            '$route.params': {
                handler(newParams, oldParams) {
                    this.showThankYou = false;
                },
                deep: true,
                immediate: true
            }
        }
    }
</script>

<style lang="scss" scoped>
    @import "../../assets/styles/variable";

    .bd-toc {
        transition: all ease 0.2s;
        transform: translateX(0);
        @include media-breakpoint-down(lg) {
            margin: $rem-1 0;
        }

        &::-webkit-scrollbar {
            display: none;
        }

        > div {

            @include media-breakpoint-up(lg) {
                position: sticky;
                top: 7rem;
                max-width: 308px;
                width: 100%;
                height: fit-content;
                overflow-x: hidden;
                overflow-y: auto;
            }
        }

        @include media-breakpoint-up(lg) {
            margin-top: 4rem;
            max-height: 100%;
            border: 0;
            border-left-width: 1px;
            border-style: solid;
            border-image: linear-gradient(to bottom, #181818, #5c5c5c, #181818) 1 100%;
            background-color: rgba($black-4, .7);
            padding-bottom: 1rem;
        }

        nav {
            padding-bottom: 1.5rem;
            border-bottom: 1px solid $black-6;
            overflow-y: auto;
            max-height: 60vh;
            overflow-x: hidden;
            position: relative;

            &::-webkit-scrollbar {
                width: 4px;
                height: 4px;
            }

            &::-webkit-scrollbar-track {
                background: transparent;
            }

            &::-webkit-scrollbar-thumb {
                background: $primary-1;
            }

            &::-webkit-scrollbar-thumb:hover {
                background: #370883;
            }

            @include font-size(.875rem);

            ul {
                margin-bottom: 0;
                list-style: none;

                &:has(a.active) {
                    .table-content {
                        a {
                            color: $purple;
                            font-weight: 500;
                            border-left-color: $purple;
                            border-left: 1px solid $purple !important;
                        }
                    }
                }

                li {
                    a {
                        border-left: 1px solid transparent;
                        padding-left: 0.75rem;
                        color: $white-1;
                        font-weight: 500;
                        cursor: pointer;

                        @for $i from 2 through 6 {
                            &.depth-#{$i} {
                                padding-left: calc(0.5rem * ($i - 2) + 2rem);
                            }
                        }

                        &:hover,
                        &.active {
                            color: $purple;
                            border-left: 1px solid $purple-36 !important;
                        }
                    }
                }
            }

            a {
                display: block;
                padding: .125rem .75rem;
                color: inherit;
                text-decoration: none;
                color: var(--bs-gray-700);
                border-left: 1px solid transparent;

                code {
                    font: inherit;
                }
            }
        }

        .h6 {
            color: $white-1;
            font-size: $font-size-sm;
            line-height: 1.875rem;
            font-weight: 600;
            padding: .5rem;
        }

        hr {
            border-color: var(--bs-gray-600);
        }
    }

    .btn {
        border: 1px solid $black-6;
        border-radius: 8px;
        text-align: center;
        width: 100%;
        display: inline-block;
        background: $black-4;
        color: var(--bs-gray-500);
        font-size: $font-size-sm;

        &.collapsed {
            border-radius: 8px 8px 0 0;
        }

        &:hover,
        &:focus,
        &:active,
        &[aria-expanded="true"] {
            background: $black-4;
            color: $white;
            font-size: 16px;
        }
    }

    .bd-toc-collapse {
        strong {
            margin-left: calc($spacer * 2);
        }

        @include media-breakpoint-down(lg) {
            nav {
                padding-bottom: $spacer;
                @include border-radius(var(--bs-border-radius));
            }
        }

        @include media-breakpoint-up(lg) {
            display: block !important; // stylelint-disable-line declaration-no-important
        }
    }

    .bd-social-list, .bd-toc-collapse {
        @include media-breakpoint-down(lg) {
            border-top-width: 0 !important;
            border: 1px solid $black-6;
            border-radius: 0 0 8px 8px;
        }

        button:hover {
            color: $purple-36 !important;
        }

        ul, :deep(ul) {
            li {
                a {
                    font-weight: 500;

                    &:hover {
                        color: $purple-36 !important;
                        border-left: 1px solid $purple-36 !important;
                    }
                }
            }
        }
    }
</style>