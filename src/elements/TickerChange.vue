<template>
  <span class="icon has-text-info">
    <span v-if="this.value === null" class="tag">N/A</span>
    <span v-else-if="this.value > 0" class="tag is-success">{{ this.number }}</span>
    <span v-else class="tag is-danger">{{ this.number }}</span>
  </span>
</template>

<script>
import NumberUtil from '@/util/number_util';

export default {
    name : 'tickerChange',
    props : {
        value : [String, Number],
        decimals : {
            type : Number,
            default : 1,
        },
        or : {
            type : String,
            default : 'N/A',
        },
    },
    computed : {
        number () {
            if(this.value === null || this.value === undefined){
                return this.or ? this.or : 'N/A';
            }
            return NumberUtil.roundToTwoDecimals(this.value * 100, this.decimals, true);
        }
    }
}
</script>
