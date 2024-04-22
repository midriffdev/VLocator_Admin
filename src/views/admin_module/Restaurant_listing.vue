<script setup lang="ts">
import { ref, computed, onMounted,onBeforeMount,watch } from 'vue';
import BaseBreadcrumb from '@/components/shared/BaseBreadcrumb.vue';
// import { useContactStore } from '@/stores/apps/contact';
import axios from 'axios';
import { useRouter } from "vue-router";
// const router = useRouter();
import BASE_URL from '@/stores/myVar';
axios.defaults.baseURL = BASE_URL
// import staffManagement from '@/_mockApis/apps/staffManagement';
// const tab = ref(null);
// const store = useContactStore();

const page = ref({ title: 'Customer' });

const breadcrumbs = ref([
    {
        text: 'Customer List',
        disabled: true,
        href: '#'
    }
]);

const token = localStorage.getItem('res_token')
const itemsPerPage = 10;
const currentPage = ref(1);
const search = ref('');

const customerList = ref([
    {
        id: '#0013',
        firstName: 'Hanna',
        lastName: 'Gover',
        phone: '8274241752',
   
    },
    
   
]);

onMounted(() => {
    fetchcustomerList();
});

const filteredList = computed(() => {
    if (!customerList.value) return []; // Check if customerList.value is defined
    return customerList.value.filter((item: any) => {
        const fullName = `${item.user?.firstName} ${item.user?.lastName}`.toLowerCase();
        const searchValue = search.value.toLowerCase();
        return fullName.includes(searchValue);
    });
});
const paginatedList = computed(() => {
    const start = (currentPage.value - 1) * itemsPerPage;
    const end = start + itemsPerPage;
    return filteredList.value.slice(start, end);
});




const fetchcustomerList = async () => {
    console.log("customer LIST API CALL")
  try {
    const token = localStorage.getItem('authToken');
    const response = await axios.post('api/staff/all_customer/',{'token':token});
    console.log('Staff API response:', response.data);
      if (response.data) {
        const updatedCustomerList = response.data.map(item => {
                return {
                    id: item.id, // Assuming the API response contains an 'id' property
                    firstName: item.user.first_name,
                    lastName: item.user.last_name,
                    phone: item.phone,
                    // Add other properties as needed
                };
            });
        customerList.value = updatedCustomerList;
      }else{
        console.log("First Error")
      }
  } catch (error) {
    console.error('API error:', error);
  }
}


function deleteItem(item: any) {
    const index = customerList.value.indexOf(item);
    console.log("INDEX",item.id)
    confirm('Are you sure you want to delete this item?') && customerList.value.splice(index, 1);
    submitDeleteForm(item.id);
}

const submitDeleteForm = async (index:any) => {
    console.log("customer Delete API CALL")
  try {
    console.log("INSIDE TRY",index)
    const token = localStorage.getItem('authToken');
    const formdata = {
        token:token,
        customer_id:index,
    }
    console.log(formdata);
    const response = await axios.post('api/staff/delete_customer/',formdata);
      console.log('Staff API response:', response.data);
        if (response.data) {
        }else{
            console.log("First Error")
        }
    } catch (error) {
        console.error('API error:', error);
        
    }
}

</script>
<template>
    <BaseBreadcrumb :title="page.title" :breadcrumbs="breadcrumbs"></BaseBreadcrumb>
    <v-card elevation="10">
        <v-card-text>
            <v-row class="align-center">
                <v-col cols="12" lg="8">
                    <v-row>
                        <v-col cols="12" md="6">
                            <v-text-field density="compact" v-model="search" label="Search Customer" hide-details variant="outlined"></v-text-field>
                        </v-col>
                        <!-- <v-col cols="12" md="6">
                                <v-select
                                :items="selectItems"
                                v-model="selectModalitm"
                                label="Select Role"
                                density="compact"
                                placeholder="Select Role"
                                hide-details
                            ></v-select>
                        </v-col> -->
                    </v-row>
                </v-col>
                
            </v-row>

            <v-table class="mt-5">
                <thead>
                    <tr>
                        <th class="text-subtitle-1 font-weight-bold">ID </th>
                        <th class="text-subtitle-1 font-weight-bold">Name</th>
                        <th class="text-subtitle-1 font-weight-bold">Phone</th>
                        <!-- <th class="text-subtitle-1 font-weight-bold">Email</th> -->
                        <th class="text-subtitle-1 font-weight-bold">Actions</th>
                    </tr>
                </thead>
                <tbody>
                    <template v-if="paginatedList.length > 0"> 
                        <tr v-for="(item,index) in paginatedList" :key="item" :currentPage="currentPage" :pageSize="10">

                            <td class="text-subtitle-1">{{ index+1 }}</td>

                            <td>
                                <div class="d-flex py-4">
                                    
                                    <router-link :to="'/customerProfile/' + token + '/' + item.id" class="text-primary text-decoration-none text-subtitle-1">
                                        <!-- <h4 class="text-h6 font-weight-semibold">{{ item.userinfo }}</h4> -->
                                        {{ item.firstName }} {{ item.lastName }}
                                    </router-link>
                                </div>
                                
                            </td>
                            <td>
                                <span class="text-subtitle-1 d-block mt-1 textSecondary">{{ item.phone }}</span>
                            </td>
                            <!-- <td>
                                <span class="text-subtitle-1 d-block mt-1 textSecondary">{{ item.Email }}</span>
                            </td> -->
                            <!-- <td>
                                <v-chip :color="item.Role == 'Manager'? 'primary' : item.Role == 'Service Staff'? 'secondary' : 'warning'" size="small" label>{{ item.Role }}</v-chip>
                            </td> -->
                            <td>
                                <v-tooltip text="Delete">
                                    <template v-slot:activator="{ props }">
                                        <v-btn icon flat @click="deleteItem(item)" v-bind="props"
                                            ><TrashIcon stroke-width="1.5" size="20" class="text-error"
                                        /></v-btn>
                                    </template>
                                </v-tooltip>
                            </td>
                        </tr>
                    </template> 
                    <template v-else>
                        <tr><td colspan="6" align="center" class="text-subtitle-1">No data Found</td></tr>
                    </template>
                </tbody>
            </v-table>
            <v-pagination v-model="currentPage" :length="Math.ceil(filteredList.length / itemsPerPage)"
                rounded="circle"></v-pagination>
        </v-card-text>
    </v-card>

</template>
