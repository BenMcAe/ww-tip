<template>
    <div class="ww-tips" :style="{ 'border-color': wwObject.content.data.borderColor }">
        <wwObject class="ww-tip-background" :ww-object="wwObject.content.data.background" ww-category="background"></wwObject>
        <div class="ww-tip-wrapper">
            <wwLayoutColumn
                tag="div"
                ww-default="ww-text"
                :ww-list="wwObject.content.data.tips"
                @ww-add="add(wwObject.content.data.tips, $event)"
                @ww-remove="remove(wwObject.content.data.tips, $event)"
                :style="{ color: wwObject.content.data.styleColor }"
            >
                <wwObject tag="div" class="ww-tip-text" v-for="tip in wwObject.content.data.tips" :key="tip.uniqueId" :ww-object="tip"></wwObject>
            </wwLayoutColumn>
        </div>
    </div>
</template>

<script>
/* wwManager:start */
wwLib.wwPopups.addStory('WWTIP_CUSTOM', {
    title: {
        en: 'Color picker',
        fr: 'Choisir une couleur',
    },
    type: 'wwPopupForm',
    storyData: {
        fields: [
            {
                label: {
                    en: 'Style Color:',
                    fr: 'Coulour du style :',
                },
                type: 'color',
                key: 'styleColor',
                // value: '#2c3e50',
                valueData: 'styleColor',
                desc: {
                    en: 'Choose a style color for the block',
                    fr: 'Changer le style de couleur',
                },
            },
            {
                label: {
                    en: 'Background Color:',
                    fr: 'Couleur du background :',
                },
                type: 'color',
                key: 'backgroundColor',
                // value: '#f3f5f7',
                valueData: 'backgroundColor',
                desc: {
                    en: 'Choose a background color for the block',
                    fr: 'Changer la couleur du background',
                },
            },
            {
                label: {
                    en: 'Border Color:',
                    fr: 'Couleur de la bordure :',
                },
                type: 'color',
                key: 'borderColor',
                // value: '#42b983',
                valueData: 'borderColor',
                desc: {
                    en: 'Choose a border color for the block',
                    fr: 'Changer la couleur de la bordure',
                },
            },
        ],
    },
    buttons: {
        NEXT: {
            text: {
                en: 'Ok',
                fr: 'Ok',
            },
            next: false,
        },
    },
});

wwLib.wwPopups.addStory('WWTIP_CODE', {
    title: {
        en: 'Choose a block type',
        fr: 'Choisissez le type du block',
    },
    type: 'wwPopupEditWwObject',
    storyData: {
        list: {
            Tips: {
                separator: {
                    en: 'Configuration',
                    fr: 'Configuration',
                },
                title: {
                    en: 'Tips',
                    fr: 'Indice',
                },
                desc: {
                    en: 'Useful clues',
                    fr: 'Donner un indice',
                },
                icon: 'fas fa-lightbulb',
                shortcut: 't',
                next: null,
                result: {
                    styleColor: '#2c3e50',
                    backgroundColor: '#f3f5f7',
                    borderColor: '#42b983',
                    title: {
                        en: 'TIP',
                        fr: 'Indice',
                    },
                },
            },
            WARNING: {
                title: {
                    en: 'Warning',
                    fr: 'Alert',
                },
                desc: {
                    en: 'Warn a user',
                    fr: 'Alerter un utilisateur',
                },
                icon: 'fas fa-exclamation-triangle',
                shortcut: 'w',
                next: null,
                result: {
                    styleColor: '#b29400',
                    backgroundColor: '#FFE5644D',
                    borderColor: '#e7c000',
                    title: {
                        en: 'WARNING',
                        fr: 'ATTENTION',
                    },
                },
            },
            Danger: {
                title: {
                    en: 'Danger',
                    fr: 'Danger',
                },
                desc: {
                    en: 'Warn a user about something critical',
                    fr: 'Alerter un utilisateur',
                },
                icon: 'fas fa-skull-crossbones',
                shortcut: 'd',
                next: null,
                result: {
                    styleColor: '#900',
                    backgroundColor: '#ffe6e6',
                    borderColor: '#c00',
                    title: {
                        en: 'WARNING',
                        fr: 'ATTENTION',
                    },
                },
            },
            Custom: {
                title: {
                    en: 'Custom',
                    fr: 'Customizer',
                },
                desc: {
                    en: 'customize the block',
                    fr: 'Customizer le block',
                },
                icon: 'fas fa-palette',
                shortcut: 'c',
                next: 'WWTIP_CUSTOM',
                result: {},
            },
        },
    },
    buttons: {
        NEXT: {
            text: {
                en: 'Ok',
                fr: 'Ok',
            },
            next: false,
        },
    },
});
/* wwManager:end */

export default {
    name: '__COMPONENT_NAME__',
    props: {
        wwObjectCtrl: Object,
        wwAttrs: {
            type: Object,
            default: {},
        },
    },
    computed: {
        wwObject() {
            return this.wwObjectCtrl.get();
        },
        /* wwManager:start */
        editMode() {
            return this.wwObjectCtrl.getSectionCtrl().getEditMode() == 'CONTENT';
        },
        /* wwManager:end */
    },
    methods: {
        init() {
            this.loaded = true;
            this.wwObject.content.data = this.wwObject.content.data || {};

            if (!this.wwObject.content.data.background) {
                this.wwObject.content.data.background = wwLib.wwObject.getDefault({
                    type: 'ww-color',
                    data: {
                        backgroundColor: '#f3f5f7',
                    },
                });
            }
            if (!this.wwObject.content.data.styleColor) {
                this.wwObject.content.data.styleColor = '#2c3e50';
            }
            if (!this.wwObject.content.data.borderColor) {
                this.wwObject.content.data.borderColor = '#42b983';
            }
            if (!this.wwObject.content.data.tips) {
                this.wwObject.content.data.tips = [];
            }
            if (_.isEmpty(this.wwObject.content.data.tips)) {
                this.wwObject.content.data.tips.push(
                    wwLib.wwObject.getDefault({
                        type: 'ww-text',
                        data: {
                            text: {
                                fr: 'INDICE',
                                en: 'TIP',
                            },
                        },
                    })
                );
                this.wwObject.content.data.tips.push(
                    wwLib.wwObject.getDefault({
                        type: 'ww-text',
                        data: {
                            text: {
                                fr: 'Pour changer le type de ce bloc, cliquez sur le bouton orange',
                                en: 'To change the type of this block click on the orange button',
                            },
                        },
                    })
                );
            }

            this.wwObjectCtrl.update(this.wwObject);
        },
        /* wwManager:start */
        add(list, options) {
            list.splice(options.index, 0, options.wwObject);
            this.wwObjectCtrl.update(this.wwObject);
        },
        remove(list, options) {
            list.splice(options.index, 1);
            this.wwObjectCtrl.update(this.wwObject);
        },
        async edit() {
            try {
                wwLib.wwObjectHover.setLock(this);
                const background = wwLib.$store.getters["websiteData/getWwObject"](this.wwObject.content.data.background.uniqueId)
                const options = {
                    firstPage: 'WWTIP_CODE',
                    data: {
                        wwObject: this.wwObject,
                        borderColor: this.wwObject.content.data.borderColor,
                        styleColor: this.wwObject.content.data.styleColor,
                        backgroundColor: (background) ? background.content.data.backgroundColor : ''
                    },
                };

                const result = await wwLib.wwPopups.open(options);
                /*=============================================m_ÔÔ_m=============================================\
                  STYLE
                \================================================================================================*/
                if (typeof result != 'undefined') {
                    if (typeof result.backgroundColor != 'undefined') {
                        this.wwObject.content.data.background = wwLib.wwObject.getDefault({
                            type: 'ww-color',
                            data: {
                                backgroundColor: result.backgroundColor,
                            },
                        });
                    }
                    if (typeof result.borderColor != 'undefined') {
                        this.wwObject.content.data.borderColor = result.borderColor;
                    }
                    if (typeof result.styleColor != 'undefined') {
                        this.wwObject.content.data.styleColor = result.styleColor;
                    }

                    if (typeof result.title != 'undefined') {
                        if (typeof this.wwObject.content.data.tips != 'undefined') {
                            this.wwObject.content.data.tips[0] = wwLib.wwObject.getDefault({
                                type: 'ww-text',
                                data: {
                                    text: result.title,
                                },
                            });
                        }
                    }
                    this.wwObjectCtrl.update(this.wwObject);
                }
                wwLib.wwObjectHover.removeLock();
            } catch (error) {
                console.error(error);
            }
        },
        /* wwManager:end */
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
    .ww-tip-background {
        position: absolute;
        width: 100%;
        height: 100%;
        top: 0;
        left: 0;
        z-index: -1;
    }
    .ww-tip-wrapper {
        width: 100%;
        height: 100%;
        min-height: 80px;
        .ww-tip-text {
            font-size: 14px;
            font-family: sans-serif;
        }
    }
}
</style>
