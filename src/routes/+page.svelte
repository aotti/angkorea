<script lang="ts">
    const stationList = ['Stasiun A', 'Stasiun B', 'Stasiun C']
    let currentStation = $state(0)

    type MouseEventType = Event & {currentTarget: EventTarget & HTMLButtonElement}
    function changeStation(ev: MouseEventType) {
        const button = ev.currentTarget
        const arrow = button.id.split('_')[1]
        switch (arrow) {
            case 'left':
                currentStation = currentStation <= 0 ? 0 : currentStation - 1
                break
            case 'right':
                currentStation = currentStation >= stationList.length-1 ? 2 : currentStation + 1
                break
        }
    }
    
    type FocusEventType = Event & {currentTarget: EventTarget & HTMLSelectElement}
    function focusTimeOrRoute(ev: FocusEventType) {
        const select = ev.currentTarget
        select.classList.add('text-black')
    }
    function blurTimeOrRoute(ev: FocusEventType) {
        const select = ev.currentTarget
        select.classList.remove('text-black')
    }
    function changeTimeOrRoute(ev: FocusEventType) {
        const select = ev.currentTarget
        select.classList.remove('text-black')
        select.blur()
    }

    import schedule from "$lib/assets/config/schedule.json"
    const scheduleList = schedule.list
</script>

<!-- html head -->
<svelte:head>
    <title> Angkorea </title>
</svelte:head>

<main class="bg-black/50">
    <div class="flex flex-col gap-4 bg-dark-blue-2 text-white border border-transparent md:border-black lg:border-black md:w-[35%] md:mx-auto lg:w-[35%] lg:mx-auto h-screen">
        <!-- title -->
        <div class="text-center py-1 border-b">
            <h1 class="text-xl"> ANGKOREA </h1>
            <i class="text-sm"> jadwal bis bukan angkot </i>
        </div>

        <!-- search -->
        <div class="p-1">
            <form class="flex justify-around" onsubmit={ev => ev.preventDefault()}>
                <!-- time input -->
                <div>
                    <label for="time_input"> waktu </label>
                    <select id="time_input" class="w-32 border rounded-md px-1 text-center" 
                    onfocus={focusTimeOrRoute} onblur={blurTimeOrRoute} onchange={changeTimeOrRoute}>
                        <option value="7"> 07:00 </option>
                        <option value="8"> 08:00 </option>
                        <option value="9"> 09:00 </option>
                        <option value="10"> 10:00 </option>
                    </select>
                </div>
                <!-- route input -->
                <div>
                    <label for="route_input"> rute </label>
                    <select id="route_input" class="w-32 border rounded-md px-1 text-center" 
                    onfocus={focusTimeOrRoute} onblur={blurTimeOrRoute} onchange={changeTimeOrRoute}>
                        <option value="a-b"> A ke B </option>
                        <option value="b-c"> B ke C </option>
                        <option value="c-d"> C ke D </option>
                    </select>
                </div>
            </form>
        </div>

        <!-- bus & route -->
        <div class="p-px text-center bg-dark-blue-4">
            <div class="grid grid-cols-4 gap-px grid-border">
                <!-- station -->
                <p class=""></p>
                <div class="station flex justify-between col-span-3">
                    <button id="station_left" class="hover:cursor-pointer" onclick={changeStation}> ◀ </button>
                    <p> {stationList[currentStation]} </p>
                    <button id="station_right" class="hover:cursor-pointer" onclick={changeStation}> ▶ </button>
                </div>
                <!-- bus -->
                <p class="bus"> Bis 1 </p>
                <p class="col-span-3"> jadwal </p>
                <p class="bus"> Bis 2 </p>
                <p class="col-span-3"> jadwal </p>
                <p class="bus"> Bis 3 </p>
                <p class="col-span-3"> jadwal </p>
            </div>
        </div>
    </div>
</main>