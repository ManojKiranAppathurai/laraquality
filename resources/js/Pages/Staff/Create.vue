<template>
    <app-layout>
        <!--Header-->
        <template #header>
           Create New Staff
        </template>
        <!--Sub Header-->
        <template #sub-header>
            You can new staff for your company
        </template>
        <!--Action Buttons-->
        <template #action-buttons>
            <action-button label="Back to Staff List" :link="route('staff.index')" action="back"/>
        </template>
        <div class="relative w-full">
            <!--Content Table-->
            <form-content @submitted="save" @reset="reset">
                <form-section
                title="Personal Infos"
                description="This information is subject to personal data protection law.">
                    <!-- Name -->
                    <input-group label="Name" labelFor="name" class="col-span-12 md:col-span-6">
                        <InputText id="name"  v-model="form.name"/>
                    </input-group>
                    <!-- Citizen Id -->
                    <input-group label="Citizen ID" labelFor="citizen_id" class="col-span-12 md:col-span-6">
                        <InputText id="citizen_id"  v-model="form.citizen_id"/>
                    </input-group>
                    <!-- Birthday -->
                    <input-group label="Birthday" labelFor="birthday_date" class="col-span-12 md:col-span-6">
                        <InputDate v-model="form.birthday_date"/>
                    </input-group>
                    <!-- Blood Group -->
                    <input-group label="Blood Group" labelFor="blood_group" class="col-span-12 md:col-span-6">
                        <Dropdown v-model="form.blood_group" :options="bloodGroup" optionLabel="name" placeholder="Select" :showClear="true">
                            <!--Chosen Item-->
                            <template #value="slotProps">
                                <div class="flex flex-row items-center" v-if="slotProps.value">
                                    <!--Name-->
                                    <div>{{slotProps.value.name}}</div>
                                </div>
                                <span v-else>
                                    {{slotProps.placeholder}}
                                </span>
                            </template>
                            <!--Item in The Opened List-->
                            <template #option="slotProps">
                                <div class="flex flex-row items-center">
                                    <!--Icon-->
                                    <component v-bind:is="slotProps.option.icon" :class="slotProps.option.class"></component>
                                    <!--Name-->
                                    <div>{{slotProps.option.name}}</div>
                                </div>
                            </template>
                        </Dropdown>
                    </input-group>
                    <!-- Phone -->
                    <input-group label="Phone" labelFor="phone" class="col-span-12 md:col-span-6">
                        <InputMask id="phone" name="phone" v-model="form.phone"  mask="+99(999) 999-9999" />
                    </input-group>
                    <!-- Address -->
                    <input-group label="Address" labelFor="address" class="col-span-12 md:col-span-6">
                        <InputText id="address"  v-model="form.address"/>
                    </input-group>
                    <!-- Emergency Contacts -->
                    <input-group label="Emergency Contacts" labelFor="emergency_contacts" class="col-span-12 md:col-span-6">
                        <InputRepeatable id="emergency_contacts"  v-model="form.emergency_contacts" value1name="Name" value2name="Phone"/>
                    </input-group>
                    <!-- Education Information -->
                    <input-group label="Education Info" labelFor="education_info" class="col-span-12 md:col-span-6">
                        <InputRepeatable id="education_info"  v-model="form.education_info" value1name="School" value2name="Section"/>
                    </input-group>
                    <!-- Skill Information -->
                    <input-group label="Skill Info" labelFor="skill_info" class="col-span-12 md:col-span-6">
                        <InputRepeatable id="skill_info"  v-model="form.skill_info" value1name="Skill"/>
                    </input-group>
                    <!-- Additional Tasks -->
                    <input-group label="Additional Tasks" labelFor="additional_task" class="col-span-12 md:col-span-6">
                        <InputRepeatable id="additional_task"  v-model="form.additional_task" value1name="Task"/>
                    </input-group>
                </form-section>
                <form-section
                title="Staff of business information"
                description="The position and other information about of the staff in the business">
                    <!-- Email -->
                    <input-group label="Email" labelFor="email" class="col-span-12 md:col-span-6">
                        <InputText id="email" name="email"  v-model="form.email"/>
                    </input-group>
                    <!-- Department -->
                    <input-group label="Department" labelFor="department_id" class="col-span-12 md:col-span-6">
                        <Dropdown v-model="form.department_id" :options="departments" optionLabel="name" :filter="true" placeholder="Select a Department" :showClear="true" />
                    </input-group>
                    <!-- Collar Type -->
                    <input-group label="Collar Type" labelFor="collar_type" class="col-span-12 md:col-span-6">
                        <Dropdown v-model="form.collar_type" :options="collarType" optionLabel="name" placeholder="Select" :showClear="true" @change="jobDescriptionChange">
                            <!--Chosen Item-->
                            <template #value="slotProps">
                                <div class="flex flex-row items-center" v-if="slotProps.value">
                                    <!--Icon-->
                                    <component v-bind:is="slotProps.value.icon" :class="slotProps.value.class"></component>
                                    <!--Name-->
                                    <div>{{slotProps.value.name}}</div>
                                </div>
                                <span v-else>
                                    {{slotProps.placeholder}}
                                </span>
                            </template>
                            <!--Item in The Opened List-->
                            <template #option="slotProps">
                                <div class="flex flex-row items-center">
                                    <!--Icon-->
                                    <component v-bind:is="slotProps.option.icon" :class="slotProps.option.class"></component>
                                    <!--Name-->
                                    <div>{{slotProps.option.name}}</div>
                                </div>
                            </template>
                        </Dropdown>
                    </input-group>
                    <!-- Job Description -->
                    <input-group label="Job Description" labelFor="job_description_id" class="col-span-12 md:col-span-6">
                        <Dropdown v-model="form.job_description_id" :options="jobDescriptions" optionLabel="name" :filter="true" placeholder="Select a Job Description" :showClear="true" :disabled="this.form.collar_type == null" />
                    </input-group>
                    <!-- Manager -->
                    <input-group label="Manager" labelFor="manager_id" class="col-span-12 md:col-span-6">
                        <Dropdown v-model="form.manager_id" :options="users" :value="id" optionLabel="id" :filter="true" placeholder="Select a Manager" :showClear="true">
                            <!--Chosen Item-->
                            <template #value="slotProps">
                                <div class="flex flex-row items-center" v-if="slotProps.value">
                                    <!--Picture-->
                                    <img v-if="slotProps.value.profile_photo_path" :src="'/storage/'+slotProps.value.profile_photo_path" class="w-6 h-6 rounded-full mr-2"/>
                                    <img v-else src="/images/general/dummy_user.svg" class="w-6 h-6 rounded-full mr-2"/>
                                    <!--Name-->
                                    <div>{{slotProps.value.name}}</div>
                                </div>
                                <span v-else>
                                    {{slotProps.placeholder}}
                                </span>
                            </template>
                            <!--Item in The Opened List-->
                            <template #option="slotProps">
                                <div class="flex flex-row items-center">
                                    <!--Picture-->
                                    <img v-if="slotProps.option.profile_photo_path" :src="'/storage/'+slotProps.option.profile_photo_path" class="w-6 h-6 rounded-full mr-2"/>
                                    <img v-else src="/images/general/dummy_user.svg" class="w-6 h-6 rounded-full mr-2"/>
                                    <!--Name-->
                                    <div>{{slotProps.option.name}}</div>
                                </div>
                            </template>
                        </Dropdown>
                    </input-group>
                    <!-- Directed Staff -->
                    <input-group label="Directed Staff" labelFor="directed_staff" class="col-span-12 md:col-span-6">
                        <MultiSelect v-model="form.directed_staff" :options="users" optionLabel="name" placeholder="Select a Staff" :showClear="true" :filter="true">
                            <!--Chosen Item-->
                            <template #value="slotProps">
                                <div class="flex flex-row items-center" v-for="option of slotProps.value" :key="option.id">
                                    <!--Picture-->
                                    <img v-if="option.profile_photo_path" :src="'/storage/'+option.profile_photo_path" class="w-6 h-6 rounded-full mr-2"/>
                                    <img v-else src="/images/general/dummy_user.svg" class="w-6 h-6 rounded-full mr-2"/>
                                    <!--Name-->
                                    <div>{{option.name}}</div>
                                </div>
                                <template v-if="!slotProps.value || slotProps.value.length === 0">
                                    Select Staff
                                </template>
                            </template>
                            <template #option="slotProps">
                                <div class="flex flex-row items-center">
                                     <!--Picture-->
                                    <img v-if="slotProps.option.profile_photo_path" :src="'/storage/'+slotProps.option.profile_photo_path" class="w-6 h-6 rounded-full mr-2"/>
                                    <img v-else src="/images/general/dummy_user.svg" class="w-6 h-6 rounded-full mr-2"/>
                                    <!--Name-->
                                    <div>{{slotProps.option.name}}</div>
                                </div>
                            </template>
                        </MultiSelect>
                    </input-group>
                </form-section>
                <form-section
                title="Employment status of the staff"
                description="Information about the starting and working status of the personnel">
                    <!-- Employment Status -->
                    <input-group label="Employment Status" labelFor="status" class="col-span-12 md:col-span-6">
                        <Dropdown v-model="form.status" :options="employmentStatus" optionLabel="name" placeholder="Select" :showClear="true" @change="jobDescriptionChange">
                            <!--Chosen Item-->
                            <template #value="slotProps">
                                <div class="flex flex-row items-center" v-if="slotProps.value">
                                    <!--Name-->
                                    <div>{{slotProps.value.name}}</div>
                                </div>
                                <span v-else>
                                    {{slotProps.placeholder}}
                                </span>
                            </template>
                            <!--Item in The Opened List-->
                            <template #option="slotProps">
                                <div class="flex flex-row items-center">
                                    <!--Icon-->
                                    <component v-bind:is="slotProps.option.icon" :class="slotProps.option.class"></component>
                                    <!--Name-->
                                    <div>{{slotProps.option.name}}</div>
                                </div>
                            </template>
                        </Dropdown>
                    </input-group>
                    <!-- Employment Start Date -->
                    <input-group label="Employment Start Date" labelFor="starting_date" class="col-span-12 md:col-span-6">
                        <InputDate v-model="form.starting_date" />
                    </input-group>
                    <!-- Employment End Date -->
                    <input-group v-if="this.form.status.value == 0 || this.form.status.value == 2" label="Finish Date of Employment" labelFor="leaving_date" class="col-span-12 md:col-span-6">
                        <InputDate v-model="form.leaving_date" />
                    </input-group>
                    <!-- Leaving Reason -->
                    <input-group v-if="this.form.status.value == 0 || this.form.status.value == 2" label="Leaving Reason" labelFor="leaving_reason" class="col-span-12 md:col-span-6">
                        <InputText id="leaving_reason"  v-model="form.leaving_reason"/>
                    </input-group>
                </form-section>
            </form-content>
            <loading-screen v-if="loading"/>
        </div>
    </app-layout>
</template>

<script>
import AppLayout from '@/Layouts/AppLayout'
import FormSection from '@/Components/Form/FormSection'
import FormContent from '@/Components/Form/FormContent'
import InputGroup from '@/Components/Form/InputGroup'
import InputDate from '@/Components/Form/Inputs/InputDate'
import InputRepeatable from '@/Components/Form/Inputs/InputRepeatable'
import Checked from '@/Components/Icons/General/Checked'
import XIcon from '@/Components/Icons/General/XIcon'
import WhiteCollar from '@/Components/Icons/General/WhiteCollar'
import BlueCollar from '@/Components/Icons/General/BlueCollar'
import ActionButton from '@/Components/Buttons/ActionButton'
import LoadingScreen from '@/Components/Misc/LoadingScreen'
/*PrimeVue Models*/
import InputText from 'primevue/inputtext'
import Dropdown from 'primevue/dropdown';
import SelectButton from 'primevue/selectbutton';
import Textarea from 'primevue/textarea';
import ProgressSpinner from 'primevue/progressspinner';
import InputMask from 'primevue/inputmask';
import Chips from 'primevue/chips';
import MultiSelect from 'primevue/multiselect';


export default {
    props: ['departments','jobDescriptions','users'],
    components: {
        AppLayout,
        FormContent,
        FormSection,
        InputGroup,
        InputDate,
        InputRepeatable,
        Checked,
        XIcon,
        WhiteCollar,
        BlueCollar,
        InputText,
        Dropdown,
        SelectButton,
        ActionButton,
        Textarea,
        ProgressSpinner,
        InputMask,
        Chips,
        MultiSelect,
        LoadingScreen,
    },
    data() {
        return {
            loading: false,
            form: this.$inertia.form({
                _method: 'POST',
                name : '',
                email : '',
                department_id : '',
                job_description_id : '',
                collar_type : '',
                manager_id : '',
                directed_staff : [],
                citizen_id : '',
                status : '',
                starting_date : '',
                birthday_date : '',
                leaving_date : '',
                leaving_reason : '',
                blood_group : '',
                phone : [],
                address : '',
                emergency_contacts : [],
                education_info : [],
                skill_info : [],
                additional_task : [],
            }),
            collarType: [
                {name: 'White Collar', value: 0, icon: 'WhiteCollar',class: 'w-5 h-5 text-gray-500 mr-2'},
                {name: 'Blue Collar', value: 1, icon: 'BlueCollar',class: 'w-5 h-5 text-blue-500 mr-2'}
            ],
            bloodGroup: [
                {name: '0-', value: 0},
                {name: '0+', value: 1},
                {name: 'A-', value: 2},
                {name: 'A+', value: 3},
                {name: 'B-', value: 4},
                {name: 'B+', value: 5},
                {name: 'AB-', value: 6},
                {name: 'AB+', value: 7},
            ],
            employmentStatus: [
                {name:"Left",value:0},
                {name:"Active",value:1},
                {name:"Fired",value:2},
                {name:"Retired",value:3}
            ],
        };
    },
    methods: {
        reset: function () {
            this.form.name = '';
            this.form.email = '';
            this.form.department_id = '';
            this.form.job_description_id = '';
            this.form.collar_type = '';
            this.form.manager_id = '';
            this.form.directed_staff = [];
            this.form.citizen_id = '';
            this.form.status = '';
            this.form.starting_date = '';
            this.form.birthday_date = '';
            this.form.leaving_date = '';
            this.form.leaving_reason = '';
            this.form.blood_group = '';
            this.form.phone = [];
            this.form.address = '';
            this.form.emergency_contacts = [];
            this.form.education_info = [];
            this.form.skill_info = [];
            this.form.additional_task = [];
        },
        save() {
            this.form.post(route('staff.store'), {
                    errorBag: 'staff',
                    preserveScroll: true,
            });
            this.loading = true;
        },
        jobDescriptionChange(){
            this.$inertia.reload( {
                method: 'get',
                data: {collarTypeId :this.form.collar_type.value},
                replace: true,
                preserveState: true,
                preserveScroll: true,
                only: ['jobDescriptions'],
            })
        }


    },
}
</script>
