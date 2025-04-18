<template>
    <div class="value-wrapper">
        <HomeCard class="card-block">
            <h3><span>Accelerate</span><br/> Time to Value</h3>
            <p class="description">Deliver in days what once took weeks</p>
            <div class="kpi-wrapper">
                <div class="kpi" v-for="value in valueSet" :key="value.subject">
                    <span class="subject">{{ value.subject }}</span>
                    <span class="kpi-number">{{ value.kpi }}</span>
                    <p class="kpi-description">{{ value.description }}</p>
                </div>
            </div>
            <NuxtLink
                class="learn-more"
                href="/use-cases/stories"
            >
                Discover our Customer Stories<ArrowRightIcon/>
            </NuxtLink>
        </HomeCard>
        <HomeCard class="card-block">
            <div class="any-spider">
                <div class="spider-box" style="top: 13.5%; left: 38.4%">
                    Business Automation
                </div>
                <div class="spider-box" style="top: 37%; left: 65%;">
                    ML/AI Orchestration
                </div>
                <div class="spider-box" style="top: 69%; left: 55%;">
                    Event-Driven Automation
                </div>
                <div class="spider-box" style="top: 69%; left: 22%;">
                    Data & API Orchestration
                </div>
                <div class="spider-box" style="top: 37%; left: 12%;">
                    Batch or<br/> Real-Time ETL
                </div>
                <img src="/landing/home/any-spider.svg" alt="Any orchestration" class="spider" />
            </div>
            <h3><span>Any Orchestration</span><br/> Use Case</h3>
            <NuxtLink
                class="learn-more"
                href="/use-cases"
            >
                Explore Use Cases<ArrowRightIcon/>
            </NuxtLink>
        </HomeCard>
    </div>
</template>

<script setup lang="ts">
import { HomeCard } from '#components';
import ArrowRightIcon from 'vue-material-design-icons/ArrowRight.vue';

const valueSet = [
    {
        subject: "Productivity",
        description: "Faster Workflow Completion",
        kpi: "10x"
    },
    {
        subject: "Efficiency",
        description: "Boost in Team Efficiency",
        kpi: "80%"
    },
    {
        subject: "Profitability",
        description: "Reduction in Operational Cost",
        kpi: "75%"
    },
    {
        subject: "Time to Market",
        description: "Deployment Speed",
        kpi: "5x"
    }
]
</script>

<style lang="scss" scoped>
    @import "../../assets/styles/variable";
    .card-block {
        position: relative;
        z-index: 1;
        padding: 2rem 0 6rem;
        h3 {
            margin: 2rem 0;
            font-size: 2.2rem;
            font-weight: 600;
            margin-bottom: .5rem;
            span {
                color: $primary;
                background: linear-gradient(90deg, #7C2EEA 0%, #658AF9 100%) no-repeat center;
                background-size: 350px;
                -webkit-background-clip: text;
                -webkit-text-fill-color: transparent;
            }
        }
        .description{
            margin-bottom: 3rem;
        }
        .learn-more{
            display: flex;
            position: absolute;
            bottom: 2rem;
            right: 2rem;
            @include media-breakpoint-up(md){
                transform: translate(-50%, 0);
                opacity: 0;
            }
            transition: all 0.2s;
        }

        &:hover{
            .learn-more{
                opacity: 1;
                transform: translate(0, 0);
            }
        }
    }
    .any-spider {
        position: relative;
        margin: 0 auto;
        width: 100%;
        z-index: 1;
        container-type: inline-size;
        .spider-box{
            position: absolute;
            width: 23%;
            height: 16%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: .6rem;
            @container (min-width: 410px) {
                padding: 0 1rem;
            }
            @container (min-width: 520px) {
                font-size: .8rem;
            }
            @container (min-width: 600px) {
                font-size: 1rem;
            }
            font-weight: 600;
            border-radius: .4rem;
            border: 1px solid transparent;
            background-clip: padding-box;
            background-color: rgba(31, 32, 40, 1);
            box-shadow: 0 0 15px rgba(255, 255, 255, .3);

            &:before {
                content: "";
                position: absolute;
                border-radius: inherit;
                top: 0;
                right: 0;
                bottom: 0;
                left: 0;
                z-index: -1;
                margin: -1px;
                background-image: linear-gradient(to bottom, rgba(156, 144, 211, 0.3137254902) 0%, #AC82D6 100%);
            }
        }
        .spider {
            width: 100%;
            margin: 0 auto;
            display: block;
        }
    }

    .kpi-wrapper{
        display: grid;
        grid-template-columns: repeat(2, 1fr);
        gap: 1rem;
        margin: 0 2rem;
        @include media-breakpoint-up(md){
            margin: 0 4rem;
        }
        position: relative;
        z-index: 1;
        .kpi{
            padding: 1rem;
            border-radius: .5rem;
            border: 1px solid transparent;
            text-align: left;
            position: relative;
            background-color: rgb(26, 28, 35);
            background-clip: padding-box;
            &:before {
                content: '';
                position: absolute;
                border-radius: inherit;
                top: 0; right: 0; bottom: 0; left: 0;
                z-index: -1;
                margin: -1px;
                background-image: linear-gradient(to bottom, #9C90D350 0%, #AC82D6 100%);
            }
            .subject{
                display: block;
                font-size: .7rem;
                font-weight: 400;
                text-transform: uppercase;
                color: #8B8B8D;
            }
            .kpi-number{
                color: #817CFF;
                font-size: 2.5rem;
                font-weight: 800;
                margin: .5rem 0;
            }
            .kpi-description{
                font-size: .7rem;
                @include media-breakpoint-up(md){
                    font-size: .9rem;
                }
                font-weight: 500;
                margin:0;
            }
        }
    }

    .value-wrapper{
        margin-top: 1rem;
        gap: 1rem;
        display: grid;
        @include media-breakpoint-up(lg){
            margin-top: 2rem;
            gap: 2rem;
            grid-template-columns: repeat(2, 1fr);
        }
    }
</style>