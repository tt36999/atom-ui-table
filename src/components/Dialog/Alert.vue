<template>
    <transition name="alert" @after-leave="afterLeave">
        <div v-show="value" class="component-alert" :style="{width: width}">
            <div class="header">
                <h1 class="title">{{title}}</h1>
            </div>
            <div class="body" v-html="text"></div>
            <div class="footer">
                <v-button @click="ok" type="ghost" style="float: right;">{{btnOkText}}</v-button>
            </div>
        </div>
    </transition>
</template>
<script>
import VButton from '../form/Button'
export default {
    name: 'Alert',

    props: {
        value: {
            type: Boolean
        },

        width: {
            default: '400px'
        },

        text: {
            type: [String, Number]
        },

        title: {
            type: String,
            default: '提示'
        },

        holdTime: {
            type: Number,
            default: -1
        },

        lock: {
            type: Boolean
        }
    },

    data() {
        return {
            timeOutTimer: null,
            intervalTimer: null,
            btnOkText: '确定',
            _holdTime: 0
        };
    },

    methods: {
        ok() {
            this.$emit('ok');
            this.$emit('input', false);
        },

        afterLeave() {
            this.$emit('after-leave');
        }
    },

    computed: {
        isShow: {
            get() {
                return this.value;
            },

            set(isShow) {
                this.$emit('input', isShow);
            }
        }
    },

    watch: {
        value(value) {
            if (value && -1 != this.holdTime) {
                clearTimeout(this.timeOutTimer);
                this.timeOutTimer = setTimeout(() => {
                    this.$emit('input', false);
                }, this.holdTime);

                // 刷新时钟
                clearTimeout(this.intervalTimer);
                this._holdTime = Math.floor(this.holdTime / 1000);
                this.btnOkText = '确定 ' + this._holdTime + 's'
                this.intervalTimer = setInterval(() => {
                    if (0 < this._holdTime) {
                        this._holdTime--;
                        this.btnOkText = '确定 ' + this._holdTime + 's'
                    }
                }, 1000);

            }
        }
    },

    components: {VButton}
}
</script>
<style scoped lang="scss">
.component-alert {
    border-radius: 4px;
    background: #fff;
    box-shadow: 1px 2px 5px rgba(0, 0, 0, .2);
    >.header {
        padding: 15px;
        >.title {
            margin: 0;
            font-size: 16px;
        }
        >.btn-close {
            position: absolute;
            top: 20px;
            right: 10px;
        }
    }
    >.body {
        padding: 15px;
    }
    >.footer {
        padding: 15px;
        overflow: hidden;
    }
}

// 动画
.alert-enter-active {
    animation: alert-in .5s;
}

.alert-leave-active {
    animation: alert-out .5s;
}

@keyframes alert-in {
    0% {
        opacity: 0;
        transform: translateY(15px);
    }
    100% {
        opacity: 1;
        transform: translateY(0);
    }
}

@keyframes alert-out {
    0% {
        opacity: 1;
        transform: translateY(0);
    }
    100% {
        opacity: 0;
        transform: translateY(15px);
    }
}
</style>
