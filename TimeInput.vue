<template id="time-input-template">
    <input
        type="text"
        :placeholder="zeroes"
        class="focus:outline-none"
        :class="outerClass"
        v-model="number"
        @blur="validateNumber"
    />
</template>
<script>
export default {
    name: 'time-input',

    template: '#time-input-template',

    inject: [
        'setWorkTimeValue',
        'getWorkTimeValue'
    ],

    props: {
        wtKey: {
            type: String,
            required: true
        },
        outerClass: {
            type: String,
            required: false
        },
        template : {
            type: String,
            required: true
        },
        variant : {
            type: String,
            required: false,
            default: 'hour',
            validator: function (value) {
                return ['hour', 'minute', 'second'].indexOf(value) !== -1
            }
        }
    },

    data: function () {
        return {
            number: '21',
            format: '',
            regex: '^',
            zeroes: '',
            validation: false,
            init: false
        }
    },

    mounted() {
        let x = 1;
        this.init = true;
        this.zeroes = this.template;
        this.zeroes = this.zeroes.replace(/X/g, () => '0');
        this.format = this.template.replace(/X+/g, () => '$' + x++);
        this.template.match(/X+/g).forEach((char, key) => {
            this.regex += '(\\d{' + char.length + '})';
        });

        this.number = this.getWorkTimeValue(this.wtKey);
        this.init = false;
    },

    watch: {
        number() {
            if (this.validation || this.init) {
                this.validation = false;
                return;
            }

            this.number = this.number
                            .replace(/[^0-9]/g, '')
                            .replace(new RegExp(this.regex, 'g'), this.format)
                            .substr(0, this.template.length);
        }
    },

    methods: {
        validateNumber: function() {
            this.validation = true;

            if (this.number.length == 1) {
                this.number = '0' + this.number;
                this.setWorkTimeValue(this.wtKey, this.number);
                return;
            }

            if (
                (this.variant == 'hour' && !/^([0-1][0-9]|2[0-3])$/.test(this.number)) ||
                (
                    (this.variant == 'minute' || this.valiant == 'second') &&
                    !/^([0-5][0-9])$/.test(this.number)
                )
            )
                this.number = '00';

            this.setWorkTimeValue(this.wtKey, this.number);
        }
    }
}
</script>
