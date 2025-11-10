<script lang="ts"> 
    import schedule from "$lib/assets/config/schedule.json"
    const portraitClass = `rounded-2xl md:w-[35%] md:mx-auto lg:w-[35%] lg:mx-auto h-screen`
    const scheduleList = schedule.list
    // states
    let currentRoute = $state(0)
    let currentStation = $state(0)

    // station
    // svelte-ignore state_referenced_locally
    let stationList = $state([] as any[])
    $effect(() => {
        stationList = scheduleList[currentRoute].route.split('-')
    })

    type MouseEventType = Event & {currentTarget: EventTarget & HTMLButtonElement}
    function changeStation(ev: MouseEventType) {
        const button = ev.currentTarget
        const arrow = button.id.split('_')[1]
        switch (arrow) {
            case 'left':
                currentStation = currentStation <= 0 ? 0 : currentStation - 1
                break
            case 'right':
                currentStation = currentStation >= stationList.length-1 ? stationList.length-1 : currentStation + 1
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
        // update current route
        switch (select.value) {
            case 'A-B': currentRoute = 0; break
            case 'B-C': currentRoute = 1; break
            case 'C-D': currentRoute = 2; break
            default: currentRoute = 0; break
        }
    }
</script>

<!-- html head -->
<svelte:head>
    <title> Angkorea </title>
</svelte:head>

<main class="bg-black/50">
    <div class={`flex flex-col gap-4 bg-dark-blue-2 text-white shadow-xl shadow-black ${portraitClass}`}>
        <!-- title -->
        <div class="text-center py-1 border-b">
            <h1 class="text-xl"> ANGKOREA </h1>
            <i class="text-sm"> jadwal bis bukan angkot </i>
        </div>

        <!-- find schedule -->
        <div class="p-1">
            <form class="flex justify-around" onsubmit={ev => ev.preventDefault()}>
                <!-- time input -->
                <div>
                    <label for="time_input"> waktu </label>
                    <select id="time_input" class="w-32 border rounded-md px-1 text-center cursor-pointer" 
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
                    <select id="route_input" class="w-32 border rounded-md px-1 text-center cursor-pointer" 
                    onfocus={focusTimeOrRoute} onblur={blurTimeOrRoute} onchange={changeTimeOrRoute}>
                        <!-- <option value="a-b"> A ke B </option>
                        <option value="b-c"> B ke C </option>
                        <option value="c-d"> C ke D </option> -->
                        {#each scheduleList as s}
                        {@const routeName = s.route.replace('-', ' ke ')}
                            <option value={s.route}> {routeName} </option>
                        {/each}
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
                    <p> {`Stasiun ${stationList[currentStation]}`} </p>
                    <button id="station_right" class="hover:cursor-pointer" onclick={changeStation}> ▶ </button>
                </div>
                <!-- bus -->
                {#if scheduleList[currentRoute].route === stationList.join('-')}
                {@const currentSchedule = scheduleList[currentRoute]}
                    {#each currentSchedule.buses as bus, i}
                        <p class="bus"> {currentSchedule.buses[i]} </p>
                        <p class="schedule col-span-3">
                            {#if currentStation === 0}
                                {`jalan ${currentSchedule.depart[i]}`}
                            {:else}
                                {`tiba ${currentSchedule.arrive[i]}`}
                            {/if}
                        </p>
                    {/each}
                {/if}
            </div>
        </div>
    </div>
</main>