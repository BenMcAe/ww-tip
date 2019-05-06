<template>
    <div class="ww-tips" :style="{'border-color':  wwObject.content.data.borderColor}">
        <!-- wwManager:start -->
        <div class="ww-tip-background" :style="{'background-color':  wwObject.content.data.backgroundColor}"></div>
        <div class="ww-tip-wrapper">
            <wwContextMenu v-if="editMode" tag="div" class="ww-tip-button" @ww-options="options()">
                <div class="wwi wwi-config"></div>
            </wwContextMenu>
            <!-- wwManager:end -->
            <wwObject tag="div" ww-default="ww-text" :ww-object="wwObject.content.data.title" class="ww-tip-tilte" :style="{'color':  wwObject.content.data.styleColor }"></wwObject>
            <wwLayoutColumn
                tag="div"
                ww-default="ww-text"
                :ww-list="wwObject.content.data.tips"
                @ww-add="add(wwObject.content.data.tips, $event)"
                @ww-remove="remove(wwObject.content.data.tips, $event)"
                :style="{'color':  wwObject.content.data.styleColor }"
            >
                <wwObject tag="div" class="ww-tip-text" v-for="tip in wwObject.content.data.tips" :key="tip.uniqueId" :ww-object="tip"></wwObject>
            </wwLayoutColumn>
        </div>
    </div>
</template>
 

<script>			
import Vue from 'vue';


export default {
    name: "__COMPONENT_NAME__",
    props: {
        wwObjectCtrl: Object,
        wwAttrs: {
            type: Object,
            default: {}
        }
    },
    data() {
        return {

        }
    },
    computed: {
        wwObject() {
            return this.wwObjectCtrl.get();
        },
        editMode() {
            return this.wwObjectCtrl.getSectionCtrl().getEditMode() == 'CONTENT'

        },



    },
    watch: {
    },
    methods: {
        init() {
            wwLib.wwObjectHover.removeLock();

            this.loaded = true
            this.wwObject.content.data = this.wwObject.content.data || {}
            if (!this.wwObject.content.data.backgroundColor) {
                this.wwObject.content.data.backgroundColor = "#f3f5f7"
            }
            if (!this.wwObject.content.data.styleColor) {
                this.wwObject.content.data.styleColor = "#2c3e50"
            }
            if (!this.wwObject.content.data.title) {
                this.wwObject.content.data.title = wwLib.wwObject.getDefault({
                    type: "ww-text",
                    data: {
                        text: {
                            fr: "INDICE",
                            en: "TIP"                        }
                    }
                })
            }
            if (!this.wwObject.content.data.borderColor) {
                this.wwObject.content.data.borderColor = "#42b983"
            }

            if (!this.wwObject.content.data.tips) {
                this.wwObject.content.data.tips = []
            }


            if (_.isEmpty(this.wwObject.content.data.tips)) {
                this.wwObject.content.data.tips.push(
                    wwLib.wwObject.getDefault({
                        type: "ww-text",
                        data: {
                            text: {
                                fr: "Pour changer le type de ce bloc, cliquez sur le bouton orange",
                                en: "To change the type of this block click on the orange button"                            }
                        }
                    }))
            }

            this.wwObjectCtrl.update(this.wwObject)
        },
        add(list, options) {
            list.splice(options.index, 0, options.wwObject);
            this.wwObjectCtrl.update(this.wwObject);
        },
        remove(list, options) {
            list.splice(options.index, 1);
            this.wwObjectCtrl.update(this.wwObject);
        },
        async options() {
            try {
                // wwLib.wwObjectHover.setLock(this);

                let options = await this.edit()
                const result = await wwLib.wwPopups.open(options);
                /*=============================================m_ÔÔ_m=============================================\
                  STYLE
                \================================================================================================*/
                if (typeof (result) != 'undefined') {
                    this.wwObject.content.data.styleColor = result.styleColor;
                    this.wwObject.content.data.backgroundColor = result.backgroundColor;
                    this.wwObject.content.data.title = result.title;
                    this.wwObject.content.data.borderColor = result.borderColor;
                    this.wwObjectCtrl.update(this.wwObject)
                }
                /*                 wwLib.wwObjectHover.removeLock();
                 */
                // this.wwObjectCtrl.globalEdit(result);

            } catch (error) {
                console.error(error);
            }

        },
        async edit() {


            wwLib.wwPopups.addStory('WWTIP_CODE', {
                title: {
                    en: 'Choose a block type',
                    fr: 'Choisissez le type du block'
                },
                type: 'wwPopupList',
                storyData: {
                    list: {
                        WARNING: {
                            title: {
                                en: 'Warning',
                                fr: 'Alert'
                            },
                            desc: {
                                en: 'Warn a user',
                                fr: 'Alerter un utilisateur'
                            },
                            icon: 'fas fa-exclamation-triangle',
                            shortcut: 'w',
                            next: null,
                            result: {
                                styleColor: "#b29400",
                                backgroundColor: "#FFE5644D",
                                borderColor: "#e7c000",
                                title: "WARNING"
                            }
                        },
                        Danger: {
                            title: {
                                en: 'Danger',
                                fr: 'Danger'
                            },
                            desc: {
                                en: 'Warn a user about something critical',
                                fr: 'Alerter un utilisateur'
                            },
                            icon: 'fas fa-skull-crossbones',
                            shortcut: 'd',
                            next: null,
                            result: {
                                styleColor: "#900",
                                backgroundColor: "#ffe6e6",
                                borderColor: "#c00",
                                title: "WARNING"
                            }
                        },
                        Tips: {
                            title: {
                                en: 'Tips',
                                fr: 'Indice'
                            },
                            desc: {
                                en: 'Useful clues',
                                fr: 'Donner un indice'
                            },
                            icon: 'fas fa-lightbulb',
                            shortcut: 't',
                            next: null,
                            result: {
                                styleColor: "#2c3e50",
                                backgroundColor: "#f3f5f7",
                                borderColor: "#42b983",
                                title: "TIP"
                            }
                        },
                    }
                },
                buttons: {

                    NEXT: {
                        text: {
                            en: 'Ok',
                            fr: 'Ok'
                        },
                        next: false
                    }
                }
            })

            let options = {
                firstPage: 'WWTIP_CODE',
                data: {
                    wwObject: this.wwObject,
                }
            }

            return options
        }

    },
    mounted() {
        this.init();
        wwLib.wwElementsStyle.applyAllStyles({
            wwObject: this.wwObject,
            lastWwObject: null,
            element: this.$el,
            noAnim: this.wwAttrs.wwNoAnim,
            noClass: false,
        });

        this.$emit('ww-loaded', this);

    },
    beforeDestroyed() {
        //window.removeEventListener('resize', this.wwOnResize);
    }
};
</script>

<style lang="scss" scoped>
.ww-tips {
    position: relative;
    width: 100%;
    height: 100%;
    padding: 1.5rem 1.5rem;
    border-left-width: 0.5rem;
    border-left-style: solid;
}
.ww-tip-wrapper {
    width: 100%;
    height: 100%;
    min-height: 80px;
}
.ww-tip-button {
    position: absolute;
    top: 0;
    left: 0;
    transform: translate(-50%, -50%);
    width: 35px;
    height: 35px;
    background-color: orange;
    border-radius: 50%;
    color: white;
    z-index: 2;
    box-shadow: 0 0 5px -1px black;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 18px;
}
.ww-tip-background {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
}
.ww-tip-tilte {
    position: relative;
    margin-bottom: 10px;
    font-weight: 600;
    font-size: 16px;
    font-family: sans-serif;
}
.ww-tip-text {
    font-size: 14px;
    font-family: sans-serif;
}
</style>
