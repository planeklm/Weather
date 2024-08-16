<script>
    import { fade, fly } from 'svelte/transition';
    import Icon from "@iconify/svelte";
    
    export let isOpen = false;
    export let toggleMenu;
    export let darkMode;
    export let toggleTheme;
    
    let menuItems = [
      { name: 'Home', icon: 'material-symbols:home-outline' },
      { name: 'Search', icon: 'material-symbols:search' },
      { name: 'About', icon: 'material-symbols:info-outline' },
      { name: 'Settings', icon: 'material-symbols:settings-outline' },
    ];
  </script>
  
  {#if isOpen}
    <div 
      class="fixed inset-0 bg-gray-800 bg-opacity-50 z-50"
      on:click={toggleMenu}
      transition:fade={{ duration: 200 }}
    >
      <div 
        class="fixed right-0 top-0 bottom-0 w-64 bg-white dark:bg-[#111111] shadow-lg"
        transition:fly={{ x: 300, duration: 300 }}
        on:click|stopPropagation
      >
        <div class="p-4">
          <button 
            on:click={toggleMenu}
            class="absolute top-4 right-4 text-gray-600 dark:text-gray-300 hover:text-gray-800 dark:hover:text-white"
          >
            <Icon icon="material-symbols:close" class="text-2xl" />
          </button>
          <h2 class="text-2xl font-bold mb-4 text-gray-800 dark:text-white">Menu</h2>
          <nav>
            <ul>
              {#each menuItems as item}
                <li class="mb-2">
                  <a 
                    href="#{item.name.toLowerCase()}" 
                    class="flex items-center p-2 rounded-md text-gray-600 dark:text-gray-300 hover:bg-gray-100 dark:hover:bg-[#252525]"
                  >
                    <Icon icon={item.icon} class="mr-2 text-xl" />
                    {item.name}
                  </a>
                </li>
              {/each}
            </ul>
          </nav>
        </div>
        <div class="absolute bottom-4 left-4">
          <button
            on:click={toggleTheme}
            class="p-2 rounded-full bg-gray-200 dark:bg-[#252525] text-gray-800 dark:text-white transition-colors duration-200"
          >
            <Icon icon={$darkMode ? "ph:sun-bold" : "ph:moon-bold"} class="text-2xl" />
          </button>
        </div>
      </div>
    </div>
  {/if}