<template>
    <u-collapsible-panel :min-width="175" class="form-panel">
        <template v-slot:header>Verified Contacts</template>
        <template v-slot:content>
            <v-row class="v-grid" :class="tmr.isEditing ? 'is-editing' : 'read-only' ">
                <div class="col-6">
                    <div class="d-flex">
                        <div class="required">
                            <label class="control-label">Verified Requested For<span v-if="!tmr.isEditing">&nbsp;</span>
                            </label>
                        </div>
                        <u-tooltip-new :width="320"
                                       position="top"
                                       :position-tooltip-to-left="true">
                            <template v-slot:tooltipAnchor>
                                <span class="icon-info"></span>
                            </template>
                            <template v-slot:tooltipContent>
                                <span>Verified contact that exists in the Unity Contacts database</span>
                            </template>
                        </u-tooltip-new>
                    </div>
                    <u-tooltip-new
                        v-if="!verifiedRequestedForContact"
                        :width-percentage="tmr.isEditing ? 100 : null"
                        :width="!tmr.isEditing ? 416 : null"
                        position="bottom"
                        header-color="warning">
                        <template v-slot:tooltipAnchor>
                            <contact-picker
                                id="tmrVerifiedRequestedForContact"
                                v-model="verifiedRequestedForContact"
                                class="missing-verified-contact"
                                :is-editing="tmr.isEditing"
                                :type="contactPickerType.Customer"
                                :validate="true"
                                :show-create-button="true"
                                :company-id="tmr.company.Company_ID"
                                :is-prepend-icon-shift-input="true"
                                :display-check="displayVerifiedRequestorCheck"
                                :display-warning="!displayVerifiedRequestorCheck"
                                :rules="[v => !!v || 'Verified Requested For is required']"
                            />
                        </template>
                        <template v-slot:tooltipHeader>
                            Select or Create Requested For Contact
                        </template>
                        <template v-slot:tooltipContent>
                            <span>Requested For Contact is required to create Service Orders</span>
                        </template>
                    </u-tooltip-new>
                    <contact-picker
                        v-else
                        id="tmrVerifiedRequestedForContact"
                        v-model="verifiedRequestedForContact"
                        :is-editing="tmr.isEditing"
                        :type="contactPickerType.Customer"
                        :validate="true"
                        :show-create-button="true"
                        :company-id="tmr.company.Company_ID"
                        :is-prepend-icon-shift-input="true"
                        :display-check="displayVerifiedRequestorCheck"
                        :display-warning="!displayVerifiedRequestorCheck"
                    />
                </div>
                <div class="col-6">
                    <div class="d-flex">
                        <div :class="{'required' :
                            tmr.onsiteContactEmail
                            || tmr.onsiteContactName
                            || tmr.onsiteContactPhone}">
                            <label class="control-label">Verified Onsite Contact<span v-if="!tmr.isEditing">&nbsp;</span>
                            </label>
                        </div>
                        <u-tooltip-new :width="320"
                                       position="top"
                                       :position-tooltip-to-left="true">
                            <template v-slot:tooltipAnchor>
                                <span class="icon-info"></span>
                            </template>
                            <template v-slot:tooltipContent>
                                <span>Verified contact that exists in the Unity Contacts database</span>
                            </template>
                        </u-tooltip-new>
                    </div>
                    <u-tooltip-new
                        v-if="!verifiedOnSiteContact"
                        :width-percentage="tmr.isEditing ? 100 : null"
                        :width="!tmr.isEditing ? 416 : null"
                        position="bottom"
                        header-color="warning">
                        <template v-slot:tooltipAnchor>
                            <contact-picker
                                id="tmrVerifiedOnsiteContact"
                                v-model="verifiedOnSiteContact"
                                class="missing-verified-contact"
                                :is-editing="tmr.isEditing"
                                :type="contactPickerType.Customer"
                                :validate="true"
                                :show-create-button="true"
                                :company-id="tmr.company.Company_ID"
                                :is-prepend-icon-shift-input="true"
                                :display-check="displayVerifiedOnsiteContactCheck"
                                :display-warning="!displayVerifiedOnsiteContactCheck"
                                :rules="tmr.onsiteContactEmail || tmr.onsiteContactName || tmr.onsiteContactPhone ? [v => !!v || 'Verified Onsite Contact is required'] : []"
                            />
                        </template>
                        <template v-slot:tooltipHeader>
                            Select or Create Onsite Contact
                        </template>
                        <template v-slot:tooltipContent>
                            <span v-if="tmr.onsiteContactEmail
                                || tmr.onsiteContactName
                                || tmr.onsiteContactPhone">Onsite Contact is required to create Service Orders</span>
                            <span v-else >Please select or create an Onsite Contact if possible</span>
                        </template>
                    </u-tooltip-new>
                    <contact-picker
                        v-else
                        id="tmrVerifiedOnsiteContact"
                        v-model="verifiedOnSiteContact"
                        :is-editing="tmr.isEditing"
                        :type="contactPickerType.Customer"
                        :validate="true"
                        :show-create-button="true"
                        :company-id="tmr.company.Company_ID"
                        :is-prepend-icon-shift-input="true"
                        :display-check="displayVerifiedOnsiteContactCheck"
                        :display-warning="!displayVerifiedOnsiteContactCheck"
                    />
                </div>
            </v-row>
        </template>
    </u-collapsible-panel>
</template>

<script lang="ts">
import Vue from "vue";
import { mapGetters, mapMutations, mapState } from "vuex";
import axios from "axios";
import UCollapsiblePanel from "@/components/UCollapsiblePanel";
import ContactPicker from "@/components/ContactPicker";
import { contactPickerType } from "@/components/ContactPicker/ContactPicker.vue";
import { ContactDto } from "@/interfaces/dto/ContactDto";
import { TmValidateItemResult } from "@/interfaces/TmValidateItemResult";
import UTooltipNew from "@/components/UTooltipNew/UTooltipNew.vue";

export default Vue.extend({
    components: {
        ContactPicker,
        UCollapsiblePanel,
        UTooltipNew
    },
    data() {
        return {
            contactPickerType,
            displayVerifiedOnsiteContactCheck: false,
            displayVerifiedRequestorCheck: false,
            verifiedOnSiteContact: null as ContactDto | null,
            verifiedRequestedForContact: null as ContactDto | null

        };
    },
    computed: {
        ...mapGetters({
            getIsEditing: "tmr/getIsEditing"
        }),
        ...mapState({
            tmr: "tmr"
        })
    },
    watch: {
        getIsEditing() {
            if (this.getIsEditing === true) {
                if (this.verifiedRequestedForContact === null && this.tmr.requestedFor) {
                    this.fetchContactByEmailRequestedFor();
                }
                if (this.verifiedOnSiteContact === null && this.tmr.onsiteContactEmail) {
                    this.fetchContactByEmailOnsite();
                }
            } else {
                if (!this.tmr.verifiedRequestorDTO || !this.tmr.verifiedRequestor) {
                    this.verifiedRequestedForContact = null;
                    this.displayVerifiedRequestorCheck = false;
                } else {
                    this.verifiedRequestedForContact = this.tmr.verifiedRequestorDTO;
                }
                if (!this.tmr.verifiedOnsiteDTO || !this.tmr.verifiedOnsite) {
                    this.verifiedOnSiteContact = null;
                    this.displayVerifiedOnsiteContactCheck = false;
                } else {
                    this.verifiedOnSiteContact = this.tmr.verifiedOnsiteDTO;
                }
            }
        },
        verifiedOnSiteContact(newValue: ContactDto) {
            if (newValue) {
                if (this.tmr.verifiedOnsiteDTO?.ContactId !== newValue.ContactId) {
                    this.setVerifiedOnsiteDTO(newValue);
                    this.setVerifiedOnsite(newValue.ContactId);
                }
                this.displayVerifiedOnsiteContactCheck = true;
            } else {
                this.displayVerifiedOnsiteContactCheck = false;
            }
        },
        verifiedRequestedForContact(newValue: ContactDto) {
            if (newValue) {
                if (this.tmr.verifiedOnSiteContact?.ContactId !== newValue.ContactId) {
                    this.setVerifiedRequestorDTO(newValue);
                    this.setVerifiedRequestor(newValue.ContactId);
                }
                this.displayVerifiedRequestorCheck = true;
            } else {
                this.displayVerifiedRequestorCheck = false;
            }
        }
    },
    mounted() {
        if (this.tmr.verifiedRequestorDTO) {
            this.verifiedRequestedForContact = this.tmr.verifiedRequestorDTO;
            this.displayVerifiedRequestorCheck = true;
        }
        if (this.tmr.verifiedOnsiteDTO) {
            this.verifiedOnSiteContact = this.tmr.verifiedOnsiteDTO;
            this.displayVerifiedOnsiteContactCheck = true;
        }
    },
    methods: {
        ...mapMutations({
            setVerifiedOnsite: "tmr/setVerifiedOnsite",
            setVerifiedOnsiteDTO: "tmr/setVerifiedOnsiteDTO",
            setVerifiedRequestor: "tmr/setVerifiedRequestor",
            setVerifiedRequestorDTO: "tmr/setVerifiedRequestorDTO"
        }),
        async fetchContactByEmail(email: String) {
            const response = await axios.post<TmValidateItemResult<ContactDto>>(`/Tmrs/${email}/contact`);
            if (response.data.success && response.data.item) {
                const contactDto = response.data.item;

                return contactDto;
            }
            return null;
        },
        fetchContactByEmailOnsite() {
            this.fetchContactByEmail(this.tmr.onsiteContactEmail).then((response) => {
                const contactThroughEmail = response;

                const contactForRequestedForIsVerified = contactThroughEmail && contactThroughEmail.ContactId > 0;
                if (contactForRequestedForIsVerified) {
                    this.verifiedOnSiteContact = contactThroughEmail;
                    this.setVerifiedOnsite(contactThroughEmail.ContactId);
                    this.setVerifiedOnsiteDTO(contactThroughEmail);
                    this.displayVerifiedOnsiteContactCheck = true;
                }
            });
        },
        fetchContactByEmailRequestedFor() {
            this.fetchContactByEmail(this.tmr.requestedFor).then((response) => {
                const contactThroughEmail = response;

                const contactForRequestedForIsVerified = contactThroughEmail && contactThroughEmail.ContactId > 0;
                if (contactForRequestedForIsVerified) {
                    this.verifiedRequestedForContact = contactThroughEmail;
                    this.setVerifiedRequestor(contactThroughEmail.ContactId);
                    this.setVerifiedRequestorDTO(contactThroughEmail);
                    this.displayVerifiedRequestorCheck = true;
                }
            });
        }
    }
});
</script>

<style scoped>
.icon-info {
    color: #757575; font-size: 14px;
}
div.is-editing div.u-tooltip-container ::v-deep div.u-tooltip.bottom {
    margin-top: -11px;
}
div.read-only div.u-tooltip-container ::v-deep div.u-tooltip.bottom {
    margin-top: 7px;
    margin-left: -8px;
}

div.u-tooltip-container ::v-deep div.missing-verified-contact div {
    border-color: #E69B12
}

div.read-only div.u-tooltip-container ::v-deep .u-tooltip-header.bottom:before {
    left: 2% !important
}
</style>
