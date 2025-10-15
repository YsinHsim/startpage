<template>
  <div class="h-screen background-container" :style="wallpaperStyle">
    <div class="top-4 right-4 fixed flex gap-2">
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
      
      <DropdownMenu>
        <DropdownMenuTrigger as-child>
          <Button size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
            <Music4 />
          </Button>
        </DropdownMenuTrigger>
        <DropdownMenuContent>
          <DropdownMenuLabel><span class="font-bold">Music Player</span></DropdownMenuLabel>
          <DropdownMenuSeparator />
          <DropdownMenuItem @click="openYoutubeMusicTab">Open in New Tab</DropdownMenuItem>
          <DropdownMenuItem @click="toggleMusicPlayer">Floating Player</DropdownMenuItem>
        </DropdownMenuContent>
      </DropdownMenu>

      <TooltipProvider>
        <Tooltip>
          <TooltipTrigger as-child>
            <Button @click="openYoutubeTab" size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
              <Youtube />
            </Button>
          </TooltipTrigger>
          <TooltipContent side="bottom">
            <span>Youtube</span>
          </TooltipContent>
        </Tooltip>
      </TooltipProvider>
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
      
      <TooltipProvider>
        <Tooltip>
          <TooltipTrigger as-child>
            <Button @click="toggleGifPlayer" size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
              <Film />
            </Button>
          </TooltipTrigger>
          <TooltipContent side="bottom">
            <span>Toggle GIF</span>
          </TooltipContent>
        </Tooltip>
      </TooltipProvider>

      <TooltipProvider>
        <Tooltip>
          <TooltipTrigger as-child>
            <Button @click="togglePosterPlayer" size="icon" variant="secondary" class="transition-all duration-500 hover:shadow-xl text-blue-900">
              <Image />
            </Button>
          </TooltipTrigger>
          <TooltipContent side="bottom">
            <span>Toggle Poster</span>
          </TooltipContent>
        </Tooltip>
      </TooltipProvider>

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
      <Dialog :open="isWallpaperListDialogOpen" @update:open="isWallpaperListDialogOpen = $event">
        <DialogContent>
          <DialogHeader>
            <DialogTitle>Saved Wallpapers</DialogTitle>
            <DialogDescription>Apply or delete your saved wallpapers from this list.</DialogDescription>
          </DialogHeader>
          <div v-if="savedWallpapers.length > 0" class="flex justify-center">
            <Carousel class="relative w-full max-w-xs">
              <CarouselContent>
                <CarouselItem>
                  <div class="p-2 flex flex-col gap-2">
                    <img :src="defaultWallpaper" alt="defaultWallpaper" class="rounded shadow-lg"/>
                    <div class="flex justify-center gap-2">
                      <Button size="sm" variant="outline" @click="applySavedWallpaper(defaultWallpaper)">Apply</Button>
                      <Button size="sm" variant="destructive" disabled>Delete</Button>
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
            <p class="italic">Currently only default wallpaper available.</p>
            <div class="flex flex-col gap-2 mt-2"> 
              <img :src="defaultWallpaper" alt="defaultWallpaper" class="rounded shadow-lg" />
            </div>
          </div>
        </DialogContent>
      </Dialog>
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
    
    <div v-if="isMusicPlayerVisible" class="floating-player shadow-2xl rounded-lg" :style="playerStyle">
      <div class="player-header" @mousedown="dragStart($event, 'music')">
        <span class="font-semibold text-sm">YouTube Music</span>
        <Button @click="hideMusicPlayer" variant="ghost" size="icon" class="h-6 w-6"><X class="h-4 w-4" /></Button>
      </div>
      <div class="player-content">
        <!-- <iframe width="100%" height="100%" src="https://www.youtube.com/embed/gi-txPVUw_E?si=SYlMkU8ANWTO5WD4" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe> -->
        <iframe width="100%" height="100%" src="https://www.youtube.com/embed/PxcGOEb6cb8?si=438376fGLgMiVzNg" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
      </div>
    </div>

    <div v-if="isGifPlayerVisible" class="floating-gif-player shadow-2xl rounded-md" :style="playerGifStyle">
      <div class="player-header" @mousedown="dragStart($event, 'gif')">
        <span class="font-semibold text-sm">Music GIF</span>
        <Button @click="hideGifPlayer" variant="ghost" size="icon" class="h-6 w-6"><X class="h-4 w-4" /></Button>
      </div>
      <div class="player-content">
        <img 
          src="https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fi.pinimg.com%2Foriginals%2F3d%2F03%2F80%2F3d0380fb32e1b11ff43384afbb93b4c5.gif&f=1&nofb=1&ipt=86d8c1043c893ce87c547a2a00b53cb4eafb5a1cf3a00ba0ad7500f5bdcc55e8" 
          alt="Music GIF"
        />
      </div>
    </div>

    <div v-if="isPosterPlayerVisible" class="floating-poster-player shadow-2xl rounded-sm" :style="posterPlayerStyle">
      <div class="player-header" @mousedown="dragStart($event, 'poster')">
        <span class="font-semibold text-sm">Poster</span>
        <Button @click="hidePosterPlayer" variant="ghost" size="icon" class="h-6 w-6"><X class="h-4 w-4" /></Button>
      </div>
      <div class="player-content">
        <!-- <img 
          src="https://lh3.googleusercontent.com/a/ACg8ocIFB4DSl1w-55nqTzVeRnRXilHK68xQvBRWlaz8gHxPag48f2KU=s576-c-no" 
          alt="Poster Image"
        /> -->
        <img 
          src="https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia.tenor.com%2Ftjy8D4yvEAIAAAAC%2Fgojo-satoru.gif&f=1&nofb=1&ipt=c447a5c9821d7e37b49ad256972223624d89278eda452142609f8532f02f7574" 
          alt="Poster Image"
        />
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
    return titleChunk ? `${titleChunk} - Startpage` : 'Chrome Dashboard';
  }
})

/* Import List (Added Film and Image icons) */
import { Wallpaper, Search, ChevronsLeftRightEllipsis, Mail, Facebook, Instagram, Sparkles, Globe, Bird, Earth, Music4, Youtube, X, Film, Image } from 'lucide-vue-next'
import { Input } from '@/components/ui/input'
import { Button } from '@/components/ui/button'
import { Dialog, DialogContent, DialogHeader, DialogTitle, DialogDescription } from '@/components/ui/dialog'
import { Carousel, CarouselContent, CarouselItem, CarouselNext, CarouselPrevious } from '@/components/ui/carousel'
import { DropdownMenu, DropdownMenuContent, DropdownMenuItem, DropdownMenuLabel, DropdownMenuSeparator, DropdownMenuTrigger } from '@/components/ui/dropdown-menu'
import { Tooltip, TooltipContent, TooltipProvider, TooltipTrigger } from '@/components/ui/tooltip'

/* Variable List */
const currentDay = ref('');
const currentTime = ref('');
let timer: NodeJS.Timeout | null = null
const searchInput = ref('')
const isWallpaperListDialogOpen = ref(false)
const isAddWallpaperDialogOpen = ref(false)
const wallpaperUrl = ref('')
const newWallpaperInput = ref('')
const defaultWallpaper = 'https://pbs.twimg.com/media/GqiNuAWXYAA0MDD?format=jpg&name=large'
const savedWallpapers = ref<string[]>([])
const searchEngines = ref([
  { name: 'Google', urlTemplate: 'https://www.google.com/search?q=', icon: Earth },
  { name: 'DuckDuckGo', urlTemplate: 'https://duckduckgo.com/?q=', icon: Bird }
])
const selectedSearchEngine = ref(searchEngines.value[0])

/* MODIFIED Player Variables */
const isMusicPlayerVisible = ref(false)
const isGifPlayerVisible = ref(false)
const isPosterPlayerVisible = ref(false)

const playerPosition = ref({ x: 50, y: 150 })
const playerGifPosition = ref({ x: 0, y: 0 }) // Will be set onMounted
const posterPlayerPosition = ref({ x: 0, y: 0 }) // Will be set onMounted

const isDragging = ref(false)
const dragOffset = ref({ x: 0, y: 0 })
const currentlyDragging = ref<'music' | 'gif' | 'poster' | null>(null)

/* Function List */
const openQwenAiTab = () => window.open('https://chat.qwen.ai/', '_blank', 'noopener,noreferrer');
const openDeepSeekTab = () => window.open('https://chat.deepseek.com/', '_blank', 'noopener,noreferrer');
const openChatGptTab = () => window.open('https://chatgpt.com/', '_blank', 'noopener,noreferrer');
const openGeminiTab = () => window.open('https://gemini.google.com/app', '_blank', 'noopener,noreferrer');
const openMyPortfolio = () => window.open('https://yasinhassim.vercel.app', '_blank', 'noopener,noreferrer');
const openYoutubeMusicTab = () => window.open('https://music.youtube.com/', '_blank', 'noopener,noreferrer');
const openYoutubeTab = () => window.open('https://www.youtube.com/', '_blank', 'noopener,noreferrer');
const openGmailTab = () => window.open('https://mail.google.com/mail/?tab=rm&ogbl', '_blank', 'noopener,noreferrer');
const openFacebookTab = () => window.open('https://www.facebook.com/', '_blank', 'noopener,noreferrer');
const openInstagramTab = () => window.open('https://www.instagram.com/', '_blank', 'noopener,noreferrer');

const setSearchEngine = (engine: typeof searchEngines.value[0]) => {
  selectedSearchEngine.value = engine
  localStorage.setItem('userSelectedEngine', engine.name)
}
const updateDateTime = () => {
  const now = new Date()
  currentDay.value = now.toLocaleDateString('en-US', { weekday: 'long' })
  currentTime.value = now.toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit', hourCycle: 'h23' })
}
const performSearch = () => {
  const query = searchInput.value.trim()
  if (query) {
    const searchUrl = `${selectedSearchEngine.value.urlTemplate}${encodeURIComponent(query)}`
    window.open(searchUrl, '_blank')
    searchInput.value = ''
  }
}
const addWallpaper = () => {
  const url = newWallpaperInput.value.trim()
  if (url) {
    try {
      new URL(url)
      if (!savedWallpapers.value.includes(url)) {
        savedWallpapers.value.push(url)
        localStorage.setItem('savedWallpapers', JSON.stringify(savedWallpapers.value))
      }
      applySavedWallpaper(url)
      newWallpaperInput.value = ''
    } catch (e) { alert('Please enter a valid URL for the wallpaper') }
  } else { alert('Please enter a wallpaper URL') }
}
const applySavedWallpaper = (url: string) => {
  wallpaperUrl.value = url
  localStorage.setItem('userActiveWallpaper', url)
}
const deleteWallpaper = (urlToDelete: string) => {
  savedWallpapers.value = savedWallpapers.value.filter(url => url !== urlToDelete)
  localStorage.setItem('savedWallpapers', JSON.stringify(savedWallpapers.value))

  if (wallpaperUrl.value === urlToDelete) {
    wallpaperUrl.value = defaultWallpaper
    localStorage.removeItem('userActiveWallpaper')
  }
}

/* MODIFIED Player Visibility Functions */
const toggleMusicPlayer = () => { isMusicPlayerVisible.value = !isMusicPlayerVisible.value }
const hideMusicPlayer = () => { isMusicPlayerVisible.value = false }
const toggleGifPlayer = () => { isGifPlayerVisible.value = !isGifPlayerVisible.value }
const hideGifPlayer = () => { isGifPlayerVisible.value = false }
const togglePosterPlayer = () => { isPosterPlayerVisible.value = !isPosterPlayerVisible.value }
const hidePosterPlayer = () => { isPosterPlayerVisible.value = false }

/* MODIFIED Drag and Drop Functions */
const dragStart = (event: MouseEvent, playerType: 'music' | 'gif' | 'poster') => {
  isDragging.value = true
  currentlyDragging.value = playerType

  const positionMap = {
    music: playerPosition,
    gif: playerGifPosition,
    poster: posterPlayerPosition,
  };

  const pos = positionMap[playerType].value;
  dragOffset.value = { x: event.clientX - pos.x, y: event.clientY - pos.y };
  
  window.addEventListener('mousemove', dragMove)
  window.addEventListener('mouseup', dragEnd)
}

const dragMove = (event: MouseEvent) => {
  if (!isDragging.value) return;
  const newPos = { x: event.clientX - dragOffset.value.x, y: event.clientY - dragOffset.value.y };

  if (currentlyDragging.value === 'music') playerPosition.value = newPos;
  else if (currentlyDragging.value === 'gif') playerGifPosition.value = newPos;
  else if (currentlyDragging.value === 'poster') posterPlayerPosition.value = newPos;
}

const dragEnd = () => {
  isDragging.value = false
  currentlyDragging.value = null
  window.removeEventListener('mousemove', dragMove)
  window.removeEventListener('mouseup', dragEnd)
}

/* Computed Properties */
const wallpaperStyle = computed(() => ({ backgroundImage: `url('${wallpaperUrl.value || defaultWallpaper}')`, backgroundSize: 'cover', backgroundPosition: 'center', backgroundRepeat: 'no-repeat' }));
const playerStyle = computed(() => ({ top: `${playerPosition.value.y}px`, left: `${playerPosition.value.x}px` }));
const playerGifStyle = computed(() => ({ top: `${playerGifPosition.value.y}px`, left: `${playerGifPosition.value.x}px` }));
const posterPlayerStyle = computed(() => ({ top: `${posterPlayerPosition.value.y}px`, left: `${posterPlayerPosition.value.x}px` }));

/* onMounted and onUnmounted */
onMounted(() => {
  updateDateTime()
  timer = setInterval(updateDateTime, 1000)

  // Set initial positions for floating elements
  const posterWidth = 250; // from CSS
  posterPlayerPosition.value = { x: (window.innerWidth / 2) - (posterWidth / 2), y: 40 };

  const gifWidth = 220; // from CSS
  const gifHeight = 220; // from CSS
  playerGifPosition.value = { x: window.innerWidth - gifWidth - 20, y: window.innerHeight - gifHeight - 20 };

  // Load saved data from localStorage
  const storedWallpapers = localStorage.getItem('savedWallpapers')
  if (storedWallpapers) {
    try { savedWallpapers.value = JSON.parse(storedWallpapers) }
    catch (e) { console.error("Failed to parse saved wallpapers:", e) }
  }

  const activeWallpaper = localStorage.getItem('userActiveWallpaper')
  if (activeWallpaper) {
    wallpaperUrl.value = activeWallpaper
  } else {
    wallpaperUrl.value = defaultWallpaper;
  }

  const savedEngineName = localStorage.getItem('userSelectedEngine')
  if (savedEngineName) {
    const foundEngine = searchEngines.value.find(e => e.name === savedEngineName)
    if (foundEngine) selectedSearchEngine.value = foundEngine;
  }
})

onUnmounted(() => {
  if (timer) clearInterval(timer);
})
</script>

<style>
/* Base styles */
body { font-family: sans-serif; margin: 0; }
.background-container { min-height: 100vh; display: flex; flex-direction: column; position: relative; z-index: 0; }
.background-container::before { content: ''; position: absolute; inset: 0; background-color: rgba(0, 0, 0, 0.3); z-index: -1; }

/* Floating Player Base Styles */
.floating-player, .floating-gif-player, .floating-poster-player {
  position: fixed;
  z-index: 1000;
  /* background-color: #212121; */
  background-color: transparent;
  /* border: 1px solid #424242; */
  display: flex;
  flex-direction: column;
}

/* Individual Player Sizes */
.floating-player { width: 350px; height: 450px; }
/* .floating-gif-player { width: 220px; height: 220px; } */
.floating-poster-player { width: 200px; height: 200px; }

.player-header {
  /* background-color: #333333; */
  background-color: #2A394C;
  color: white;
  padding: 4px 8px;
  cursor: move;
  display: flex;
  justify-content: space-between;
  align-items: center;
  user-select: none;
}
.player-content {
  flex-grow: 1;
  /* background-color: #181818; */
  background-color: transparent;
}
.player-content img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
}
</style>