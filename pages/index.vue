
<template>
    <div>

        <div class="flex flex-col text-center items-center justify-center gap-6 m-10">

            <div class="flex max-w-screen-md shadow-lg w-full p-4 rounded-lg gap-4 grid-cols-12">
                <div @click="tabSelector(1)" :class="selected == 1 ? 'bg-blue-500  text-white' : 'bg-white text-blue-500 '"
                    class=" cursor-pointer p-x w-full col-span-12 lg:col-span-6 p-4 border border-blue-500 rounded-lg text-2xl duration-500 ">
                    Question 1</div>
                <div @click="tabSelector(2)" :class="selected == 2 ? 'bg-blue-500  text-white' : 'bg-white text-blue-500 '"
                    class=" cursor-pointer p-x w-full col-span-12 lg:col-span-6 p-4 border border-blue-500 rounded-lg text-2xl duration-500 ">
                    Question 2</div>

            </div>

            <div class='w-full flex max-w-screen-md px-10 py-8 mx-auto bg-white rounded-lg shadow-xl'>
                <div v-if="selected == 1" class='w-full space-y-6  flex flex-col justify-center'>

                    <div class="relative w-full  bg-slate-100">

                        <input v-motion-slide-bottom type="search" v-model="tag"
                            class="block p-4 pl-10 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-blue-500 focus:ring-blue-500 focus:border-blue-500 dark:bg-blue-200/30 dark:border-blue-300 dark:placeholder-gray-400 dark:text-gray-800 dark:focus:ring-blue-500 dark:focus:border-blue-500"
                            placeholder="Please any html tag" required>
                        <button v-motion-slide-bottom type="submit" @click="displayCheck()"
                            class="text-white absolute right-2.5 bottom-2.5 bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-2 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">Check
                            Tag</button>

                    </div>
                    <div v-motion-slide-bottom class="text-4xl p-10 bg-slate-200 rounded-lg text-blue-500 font-medium">
                        {{ output }}
                    </div>

                </div>

                <div v-if="selected == 2" class='w-full space-y-6 flex flex-col justify-center'>

                    <div class="  space-y-3 ">

                        <input v-motion-slide-bottom v-model="company"
                            class="block p-4 pl-10 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-blue-500 focus:ring-blue-500 focus:border-blue-500 dark:bg-blue-200/30 dark:border-blue-300 dark:placeholder-gray-400 dark:text-gray-900 dark:focus:ring-blue-500 dark:focus:border-blue-500"
                            placeholder="Please enter any company namae" required>
                        <input v-motion-slide-bottom v-model="score"
                            class="block p-4 pl-10 w-full text-sm text-gray-900 bg-gray-50 rounded-lg border border-blue-500 focus:ring-blue-500 focus:border-blue-500 dark:bg-blue-200/30 dark:border-blue-300 dark:placeholder-gray-400 dark:text-gray-900 dark:focus:ring-blue-500 dark:focus:border-blue-500"
                            placeholder="Please enterscore" required>
                        <button v-motion-slide-bottom type="submit" @click="addCompanyScore()"
                            class="text-white  w-full bg-green-500 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-4 py-3 dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">Add
                            Company</button>

                    </div>
                    <div>
                            <img scr="~/images/nodata.png" />
                        </div>
                    <div class="flex flex-col bg-slate-200 w-full rounded-lg ">
                        <div class="text-xl flex justify-between grid-cols-12 p-4 border-b-2 text-blue-900 font-medium">
                            <div class="col-span-12 lg:col-span-4">Company name</div>
                            <div class="col-span-12 lg:col-span-4">CSAT</div>
                        </div>
                        
                        <div v-motion-slide-bottom v-for="company in companyList" :key="company"
                            class="font-medium flex justify-between grid-cols-12 px-4 py-2 ">
                            <div class="w-full text-blue-600 col-span-12 text-left flex  font-medium px-1 lg:col-span-6"> {{
                                company.name }}</div>
                            <div :class="scoreColor(company.score)"
                                class="w-full col-span-12 lg:col-span-6 font-medium  text-right "> {{ company.score }}</div>
                        </div>
                    </div>



                </div>
            </div>
        </div>
    </div>
</template>
<script setup >
import { ref } from 'vue';
definePageMeta(() => {
    layout: "default"
})
let tag = ref('')
let company = ref('')
let score = ref('')
let output = ref('Check Output  ')
let selected = ref(1)
let companyList = ref([
])

function checkTags(tag) {

    let stack = []
    for (let i = 0; i < tag.length; i++) {
        let x = tag[i]
        if (x == '<' || x == '{' || x == '/') {
            stack.push(x)
            continue
        }
        if (stack.length == 0) {
            return false;
        }
        let check;
        switch (x) {
            case '>':
                check = stack.pop();
                if (check == '{') {
                    return false;
                }
                break;
            case '}':
                check = stack.pop();
                if (check == '<') {
                    return false;
                }

                break;
            case '/':
                check = stack.pop();
                if (check == '{' || check == '/') {
                    return false;
                }

                break;
        }
    }
    return (stack.length == 0);

}

function scoreColor(item) {
    if (item >= 90) {
        return 'text-green-500';
    } else if (item >= 65 && item < 90) {
        return 'text-blue-500';
    } else {
        return 'text-orange-500';
    }
}
function tabSelector(tab) {
    selected.value = tab;
}

function displayCheck() {

    console.log(checkTags(tag.value))
    if (checkTags(tag.value)) {
        output.value = "Tag is Balanced"
    } else {
        output.value = "Tag is Not Balanced"
    }
}
function addCompanyScore() {
    companyList.value.push({ name: company.value, score: score.value })

    console.log(companyList.value)
    companyList.value.sort(({ score: a }, { score: b }) => b - a)
    company.value = ''
    score.value = ''
}
</script>
 