<template>
    <span>{{this.number}}</span>
</template>

<script>
import NumberUtil from '@/util/number_util';

export default {
    name : 'money',
    props : {
        value : [String, Number],
        decimals : {
            type : Number,
            default : 2,
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
            return '$' + NumberUtil.withCommas(this.value, this.decimals, true);
        }
    }
}
</script>
