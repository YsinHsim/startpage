<template>
  <div class="h-screen background-container" :style="wallpaperStyle">
    <div class="top-4 right-4 fixed flex gap-2">
      <!-- AI Dropdown Menu -->
      <DropdownMenu>
        <DropdownMenuTrigger>
          <Button size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
            <Sparkles />
          </Button>
        </DropdownMenuTrigger>
        <DropdownMenuContent>
          <DropdownMenuLabel><span class="font-bold">AI Selection</span></DropdownMenuLabel>
          <DropdownMenuSeparator />
          <DropdownMenuItem @click="openGeminiTab">Gemini</DropdownMenuItem>
          <DropdownMenuItem @click="openChatGptTab">ChatGpt</DropdownMenuItem>
          <DropdownMenuItem @click="openDeepSeekTab">DeepSeek</DropdownMenuItem>
          <DropdownMenuItem @click="openQwenAiTab">Qwen AI</DropdownMenuItem>
        </DropdownMenuContent>
      </DropdownMenu>
      <!-- Music Button -->
      <TooltipProvider>
        <Tooltip>
          <TooltipTrigger as-child>
            <Button @click="openYoutubeMusicTab" size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
              <Music4 />
            </Button>
          </TooltipTrigger>
          <TooltipContent side="bottom">
            <span>Music</span>
          </TooltipContent>
        </Tooltip>
      </TooltipProvider>
      <!-- Search Engine Dropdown Menu -->
      <DropdownMenu>
        <DropdownMenuTrigger as-child>
          <Button size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
            <component :is="selectedSearchEngine.icon" class="w-5 h-5" />
          </Button>
        </DropdownMenuTrigger>
        <DropdownMenuContent>
          <DropdownMenuLabel><span class="font-bold">Search Engine</span></DropdownMenuLabel>
          <DropdownMenuSeparator />
          <DropdownMenuItem 
            v-for="engine in searchEngines" 
            :key="engine.name" 
            @click="setSearchEngine(engine)"
          >
            <component :is="engine.icon" class="w-4 h-4 mr-2" />
            {{ engine.name }}
          </DropdownMenuItem>
        </DropdownMenuContent>
      </DropdownMenu>
      <!-- Social Button -->
      <TooltipProvider>
        <Tooltip>
          <TooltipTrigger as-child>
            <Button @click="openFacebookTab" size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
              <Facebook />
            </Button>
          </TooltipTrigger>
          <TooltipContent side="bottom">
            <span>Facebook</span>
          </TooltipContent>
        </Tooltip>
      </TooltipProvider>
      <TooltipProvider>
        <Tooltip>
          <TooltipTrigger as-child>
            <Button @click="openInstagramTab" size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
              <Instagram />
            </Button>
          </TooltipTrigger>
          <TooltipContent size="bottom">
            <span>Instagram</span>
          </TooltipContent>
        </Tooltip>
      </TooltipProvider>
      <!-- Wallpaper Dropdown Menu -->
      <DropdownMenu>
        <DropdownMenuTrigger>
          <Button size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
            <Wallpaper />
          </Button>
        </DropdownMenuTrigger>
        <DropdownMenuContent>
          <DropdownMenuLabel><span class="font-bold">Wallpaper Menu</span></DropdownMenuLabel>
          <DropdownMenuSeparator />
          <DropdownMenuItem @click="isWallpaperListDialogOpen = true">Wallpaper List</DropdownMenuItem>
          <DropdownMenuItem @click="isAddWallpaperDialogOpen = true">Add Wallpaper</DropdownMenuItem>
        </DropdownMenuContent>
      </DropdownMenu>
      <!-- Dialog for Add Wallpaper -->
      <Dialog :open="isAddWallpaperDialogOpen" @update:open="isAddWallpaperDialogOpen = $event">
        <DialogContent>
          <DialogHeader>
            <DialogTitle>Add Wallpaper</DialogTitle>
          </DialogHeader>

          <div class="flex flex-col gap-2">
            <Input id="add-image-url" type="text" v-model="newWallpaperInput" @keydown.enter="addWallpaper" placeholder="Enter image url.." />
            <Button @click="addWallpaper">Add Wallpaper</Button>
          </div>

        </DialogContent>
      </Dialog>
      <!-- Dialog for Wallpaper List -->
      <Dialog :open="isWallpaperListDialogOpen" @update:open="isWallpaperListDialogOpen = $event">
        <DialogContent>
          <DialogHeader>
            <DialogTitle>Saved Wallpapers</DialogTitle>
            <DialogDescription>
              Apply or delete your saved wallpapers from this list.
            </DialogDescription>
          </DialogHeader>
          
          <div v-if="savedWallpapers.length > 0" class="flex justify-center">
            <Carousel class="relative w-full max-w-xs">
              <CarouselContent>
                <CarouselItem>
                  <div class="p-2 flex flex-col gap-2">
                    <img :src="defaultWallpaper" alt="defaultWallpaper" class="rounded shadow-lg"/>
                    <div class="flex justify-center gap-2">
                      <Button size="sm" variant="outline" @click="applySavedWallpaper(url)">Apply</Button>
                      <Button size="sm" variant="destructive" disabled @click="deleteWallpaper(url)">Delete</Button>
                    </div>
                  </div>
                </CarouselItem>
                <CarouselItem v-for="(url , index) in savedWallpapers" :key="index">
                  <div class="p-2 flex flex-col gap-2">
                    <img :src="url" alt="Wallpaper" class="rounded shadow-lg"/>
                    <div class="flex justify-center gap-2">
                      <Button size="sm" variant="outline" @click="applySavedWallpaper(url)">Apply</Button>
                      <Button size="sm" variant="destructive" @click="deleteWallpaper(url)">Delete</Button>
                    </div>
                  </div>
                </CarouselItem>
              </CarouselContent>
              <CarouselPrevious />
              <CarouselNext />
            </Carousel>
          </div>
          <div v-else class="text-center text-gray-500">
            <!-- No wallpapers saved yet. -->
            <p class="italic">Currently only default wallpaper available.</p>
            <div class="flex flex-col gap-2 mt-2"> 
              <img :src="defaultWallpaper" alt="defaultWallpaper" class="rounded shadow-lg" />
            </div>
          </div>

        </DialogContent>
      </Dialog>
      <!-- Email Button -->
      <TooltipProvider>
        <Tooltip>
          <TooltipTrigger as-child>
            <Button @click="openGmailTab" size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
              <Mail />
            </Button>
          </TooltipTrigger>
          <TooltipContent side="bottom">
            <span>Gmail</span>
          </TooltipContent>
        </Tooltip>
      </TooltipProvider>
    </div>
    <div class="flex justify-center items-center h-full shadow-lg">
      <!-- Search Card -->
      <div class="p-4 w-2/5 shadow-2xl rounded-sm bg-white">
        <div class="mb-8 mt-4 text-center">
          <p class="font-bold text-3xl">{{ currentTime }}</p>
          <p class="font-extralight text-2xl mb-4">{{ currentDay }}</p>
        </div>
        <div class="flex gap-2 w-full">
          <Input type="text" v-model="searchInput" @keydown.enter="performSearch" placeholder="Search" />
          <Button @click="performSearch">
            <Search class="w-5 h-5 mr-1" /> Search
          </Button>
        </div>
      </div>
    </div>
    <TooltipProvider>
      <Tooltip>
        <TooltipTrigger as-child>
          <Button @click="openMyPortfolio" size="icon" variant="ghost" class="transition-all duration-500 hover:shadow-xl hover:bg-white/10 fixed bottom-2 left-2">
            <ChevronsLeftRightEllipsis />
          </Button>
        </TooltipTrigger>
        <TooltipContent side="right">
          <span>About Developer</span>
        </TooltipContent>
      </Tooltip>
    </TooltipProvider>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, computed } from 'vue'

useHead({
  titleTemplate: (titleChunk) => {
    return titleChunk ? `${titleChunk} - Startpage` : 'Meow Startpage';
  }
})

/* Import List */
import { Wallpaper, Search, ChevronsLeftRightEllipsis, Mail, Facebook, Instagram, Sparkles, Globe, Bird, Earth, Music4 } from 'lucide-vue-next'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Dialog, DialogContent, DialogHeader, DialogTitle, DialogTrigger } from '@/components/ui/dialog'
import { Accordion, AccordionContent, AccordionItem, AccordionTrigger } from '@/components/ui/accordion'
import { Carousel, CarouselContent, CarouselItem, CarouselNext, CarouselPrevious } from '@/components/ui/carousel'
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuLabel,
  DropdownMenuSeparator,
  DropdownMenuTrigger,
} from '@/components/ui/dropdown-menu'
import {
  Tooltip,
  TooltipContent,
  TooltipProvider,
  TooltipTrigger
} from '@/components/ui/tooltip'

/* Variable List */
const currentDay = ref('');
const currentTime = ref('');
let timer: NodeJS.Timeout | null = null
const searchInput = ref('')
const defaultAccordianValue = 'wallpaperList'
const isWallpaperListDialogOpen = ref(false)
const isAddWallpaperDialogOpen = ref(false)

const wallpaperUrl = ref('') // Stores the active wallpaper URL
const newWallpaperInput = ref('') // Input for adding new wallpaper
const defaultWallpaper = 'https://pbs.twimg.com/media/GqiNuAWXYAA0MDD?format=jpg&name=large'

// Array to store all saved wallpaper URLs
const savedWallpapers = ref<string[]>([])

const searchEngines = ref([
  { name: 'Google', urlTemplate: 'https://www.google.com/search?q=', icon: Earth },
  { name: 'DuckDuckGo', urlTemplate: 'https://duckduckgo.com/?q=', icon: Bird }
])
const selectedSearchEngine = ref(searchEngines.value[0])

/* Function List */
const openQwenAiTab = () => {
  const qwenAiTabUrl = 'https://chat.qwen.ai/'
  window.open(qwenAiTabUrl, '_blank', 'noopener,noreferrer')
}
const openDeepSeekTab = () => {
  const deepSeekTabUrl = 'https://chat.deepseek.com/'
  window.open(deepSeekTabUrl, '_blank', 'noopener,noreferrer')
}
const openChatGptTab = () => {
  const chatGptTabUrl = 'https://chatgpt.com/'
  window.open(chatGptTabUrl, '_blank', 'noopener,noreferrer')
}
const openGeminiTab = () => {
  const geminiTabUrl = 'https://gemini.google.com/app'
  window.open(geminiTabUrl, '_blank', 'noopener,noreferrer')
}
const openMyPortfolio = () => {
  const portfolioUrl = 'https://yasinhassim.vercel.app'
  window.open(portfolioUrl, '_blank', 'noopener,noreferrer')
}
const openYoutubeMusicTab = () => {
  const youtubeMusicUrl = 'https://music.youtube.com/'
  window.open(youtubeMusicUrl, '_blank', 'noopener,noreferrer')
}
const openGmailTab = () => {
  const gmailTabUrl = 'https://mail.google.com/mail/?tab=rm&ogbl'
  window.open(gmailTabUrl, '_blank', 'noopener,noreferrer')
}
const openFacebookTab = () => {
  const facebookUrlTab = 'https://www.facebook.com/'
  window.open(facebookUrlTab, '_blank', 'noopener,noreferrer')
}
const openInstagramTab = () => {
  const instagramUrlTab = 'https://www.instagram.com/'
  window.open(instagramUrlTab, '_blank', 'noopener,noreferrer')
}

const setSearchEngine = (engine: typeof searchEngines.value[0]) => {
  selectedSearchEngine.value = engine
  // Save the user's choice to local storage so it's remembered
  localStorage.setItem('userSelectedEngine', engine.name)
}

const updateDateTime = () => {
  const now = new Date()

  // Get current day (e.g., "Monday", "Tuesday")
  currentDay.value = now.toLocaleDateString('en-US', { weekday: 'long' })

  // Get current time in HH:MM (24-hour) format
  currentTime.value = now.toLocaleTimeString('en-US', {
    hour: '2-digit',
    minute: '2-digit',
    hourCycle: 'h23' // Ensures 24-hour format
  })
}

const performSearch = () => {
  const query = searchInput.value.trim()

  if (query) {
    // Use the urlTemplate from the *selected* search engine
    const searchUrl = `${selectedSearchEngine.value.urlTemplate}${encodeURIComponent(query)}`
    
    // Open the URL in a new tab/window
    window.open(searchUrl, '_blank')
    searchInput.value = ''
  }
}

// Function to add a new wallpaper to the list
const addWallpaper = () => {
  const url = newWallpaperInput.value.trim()
  if (url) {
    try {
      new URL(url) // Basic URL validation
      if (!savedWallpapers.value.includes(url)) { // Avoid duplicates
        savedWallpapers.value.push(url)
        // NEW TRY-CATCH FOR LOCALSTORAGE.SETITEM
        try {
          localStorage.setItem('savedWallpapers', JSON.stringify(savedWallpapers.value))
          console.log('addWallpaper: "savedWallpapers" set in localStorage to:', JSON.stringify(savedWallpapers.value));
        } catch (storageError) {
          console.error('addWallpaper: Error saving "savedWallpapers" to localStorage:', storageError);
          alert('Failed to save wallpaper list. Your browser storage might be full or restricted.');
        }
      }
      applySavedWallpaper(url) // Apply the newly added wallpaper
      newWallpaperInput.value = '' // Clear the input field
    } catch (e) {
      alert('Please enter a valid URL for the wallpaper')
      console.error('Invalid URL or other error in addWallpaper:', e)
    }
  } else {
    alert('Please enter a wallpaper URL')
  }
}

// Function to apply a wallpaper from the saved list
const applySavedWallpaper = (url: string) => {
  wallpaperUrl.value = url
  localStorage.setItem('userActiveWallpaper', url)
  // NEW DIAGNOSTIC LOG: Confirm userActiveWallpaper set operation
  console.log('applySavedWallpaper: "userActiveWallpaper" set in localStorage to:', url);
}

// Function to delete a wallpaper from the list
const deleteWallpaper = (urlToDelete: string) => {
  savedWallpapers.value = savedWallpapers.value.filter(url => url !== urlToDelete)
  localStorage.setItem('savedWallpapers', JSON.stringify(savedWallpapers.value)) // Save the updated array
  console.log('deleteWallpaper: "savedWallpapers" updated in localStorage to:', JSON.stringify(savedWallpapers.value));

  // If the deleted wallpaper was the currently active one, set to default
  if (wallpaperUrl.value === urlToDelete) {
    wallpaperUrl.value = defaultWallpaper
    localStorage.removeItem('userActiveWallpaper') // Clear active wallpaper from storage
    console.log('deleteWallpaper: "userActiveWallpaper" removed or set to default.');
  }
  // If the list becomes empty and it's not already default, reset to default
  if (savedWallpapers.value.length === 0 && wallpaperUrl.value !== defaultWallpaper) {
      wallpaperUrl.value = defaultWallpaper;
      localStorage.removeItem('userActiveWallpaper');
      console.log('deleteWallpaper: "userActiveWallpaper" removed due to empty list.');
  }
}


/* Computed Property for Wallpaper Style */
const wallpaperStyle = computed(() => {
  const imageUrl = wallpaperUrl.value || defaultWallpaper // Use default if wallpaperUrl is empty
  return {
    backgroundImage: `url('${imageUrl}')`,
    backgroundSize: 'cover',
    backgroundPosition: 'center',
    backgroundRepeat: 'no-repeat',
    filter: 'brightness(0. 2)' // Example: makes background slightly darker
  }
})


/* onMounted List */
onMounted(() => {
  // Update immediately when component mounts
  updateDateTime()
  // Update every second for a live clock effect
  timer = setInterval(updateDateTime, 1000)

  // Load saved wallpapers list from local storage
  const storedWallpapers = localStorage.getItem('savedWallpapers')
  console.log('onMounted: Value retrieved from localStorage for "savedWallpapers":', storedWallpapers); // Diagnostic log here
  if (storedWallpapers) {
    try {
      const parsedWallpapers = JSON.parse(storedWallpapers)
      console.log('onMounted: Successfully parsed "savedWallpapers". Data:', parsedWallpapers);
      if (Array.isArray(parsedWallpapers)) {
        savedWallpapers.value = parsedWallpapers
        console.log('onMounted: savedWallpapers ref value updated to:', savedWallpapers.value)
      }
    } catch (e) {
      console.error("onMounted: Failed to parse saved wallpapers from localStorage:", e)
      localStorage.removeItem('savedWallpapers') // Clear corrupted data
      console.log('onMounted: "savedWallpapers" removed from localStorage due to parse error.');
    }
  } else {
    console.log('onMounted: "savedWallpapers" was null or empty in localStorage.');
  }

  // Load the currently active wallpaper
  const activeWallpaper = localStorage.getItem('userActiveWallpaper')
  console.log('onMounted: Value retrieved from localStorage for "userActiveWallpaper":', activeWallpaper);
  if (activeWallpaper) {
    wallpaperUrl.value = activeWallpaper
  } else {
    // If no active wallpaper set, but there are saved ones, apply the first one
    if (savedWallpapers.value.length > 0) {
      applySavedWallpaper(savedWallpapers.value[0]);
      console.log('onMounted: No active wallpaper, applying first saved wallpaper.');
    } else {
      wallpaperUrl.value = defaultWallpaper; // Fallback to default if no saved or active
      console.log('onMounted: No active or saved wallpapers, applying default wallpaper.');
    }
  }

  const savedEngineName = localStorage.getItem('userSelectedEngine')
  if (savedEngineName) {
    const foundEngine = searchEngines.value.find(e => e.name === savedEngineName)
    if (foundEngine) {
      selectedSearchEngine.value = foundEngine
    }
  }
})

// When the component is unmounted (removed from the page)
onUnmounted(() => {
  // Clear the interval to prevent memory leaks
  if (timer) {
    clearInterval(timer)
  }
})
</script>

<style>
/* Base styles for the page */
body {
  font-family: sans-serif;
  margin: 0;
}

/* Ensure the background container always covers the screen */
.background-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  position: relative;
  z-index: 0;
}

/* Adds a subtle dark overlay for better text readability on varying wallpapers */
.background-container::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.3); /* Dark overlay */
  z-index: -1; /* Ensure overlay is behind content but in front of background image */
}

/* Custom button primary color */
.primaryColor {
  background-color: #0284c7; /* Tailwind's sky-600 */
  transition: background-color 0.3s ease; /* Smooth transition for hover */
}

.primaryColor:hover {
  background-color: #0369a1; /* Tailwind's sky-700 */
}

/* Additional utility class (if needed for labels, etc.) */
.labelColor {
  color: #424949;
}

</style>