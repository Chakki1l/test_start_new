# ПРИМЕРЫ КОДА ДЛЯ КЛЮЧЕВЫХ КОМПОНЕНТОВ

## 1. КОЛЕСО ДЕСЯТИБОРЬЯ (React Component)

```tsx
// components/interactive/WheelOfDecathlon.tsx
'use client'

import { useState } from 'react'
import { motion, AnimatePresence } from 'framer-motion'

interface Segment {
  id: string
  name: string
  category: 'foundation' | 'heart' | 'mind' | 'purpose'
  progress: number
  angle: number
  color: string
}

const SEGMENTS: Segment[] = [
  { id: 'sleep', name: 'Сон', category: 'foundation', progress: 0, angle: 0, color: '#3b82f6' },
  { id: 'nutrition', name: 'Питание', category: 'foundation', progress: 0, angle: 36, color: '#3b82f6' },
  { id: 'movement', name: 'Движение', category: 'foundation', progress: 0, angle: 72, color: '#3b82f6' },
  { id: 'partner', name: 'Партнёр', category: 'heart', progress: 0, angle: 108, color: '#ec4899' },
  { id: 'family', name: 'Род', category: 'heart', progress: 0, angle: 144, color: '#ec4899' },
  { id: 'tribe', name: 'Стая', category: 'heart', progress: 0, angle: 180, color: '#ec4899' },
  { id: 'meaning', name: 'Смыслы', category: 'purpose', progress: 0, angle: 216, color: '#f59e0b' },
  { id: 'creativity', name: 'Творчество', category: 'purpose', progress: 0, angle: 252, color: '#f59e0b' },
  { id: 'money', name: 'Деньги', category: 'purpose', progress: 0, angle: 288, color: '#f59e0b' },
  { id: 'legacy', name: 'Наследие', category: 'mind', progress: 0, angle: 324, color: '#8b5cf6' },
]

interface WheelProps {
  userProgress?: Record<string, number>
  onSegmentClick?: (segmentId: string) => void
}

export function WheelOfDecathlon({ userProgress, onSegmentClick }: WheelProps) {
  const [hoveredSegment, setHoveredSegment] = useState<string | null>(null)
  
  // Merge user progress with default segments
  const segments = SEGMENTS.map(segment => ({
    ...segment,
    progress: userProgress?.[segment.id] ?? 0
  }))
  
  return (
    <div className="relative w-full max-w-[600px] mx-auto aspect-square">
      {/* SVG Wheel */}
      <svg 
        viewBox="0 0 200 200" 
        className="w-full h-full"
        style={{ transform: 'rotate(-90deg)' }}
      >
        {/* Background circle */}
        <circle
          cx="100"
          cy="100"
          r="80"
          fill="none"
          stroke="#e5e7eb"
          strokeWidth="40"
        />
        
        {/* Segments */}
        {segments.map((segment) => (
          <SegmentPath
            key={segment.id}
            segment={segment}
            isHovered={hoveredSegment === segment.id}
            onMouseEnter={() => setHoveredSegment(segment.id)}
            onMouseLeave={() => setHoveredSegment(null)}
            onClick={() => onSegmentClick?.(segment.id)}
          />
        ))}
        
        {/* Center circle */}
        <circle
          cx="100"
          cy="100"
          r="40"
          fill="white"
          stroke="#6366f1"
          strokeWidth="2"
        />
        
        {/* Center text */}
        <text
          x="100"
          y="95"
          textAnchor="middle"
          className="text-xs font-bold fill-gray-900"
          style={{ transform: 'rotate(90deg)', transformOrigin: '100px 100px' }}
        >
          СЧАСТЬЕ
        </text>
        <text
          x="100"
          y="110"
          textAnchor="middle"
          className="text-2xl font-bold fill-indigo-600"
          style={{ transform: 'rotate(90deg)', transformOrigin: '100px 100px' }}
        >
          {Math.round(segments.reduce((acc, s) => acc + s.progress, 0) / segments.length)}%
        </text>
      </svg>
      
      {/* Tooltip */}
      <AnimatePresence>
        {hoveredSegment && (
          <SegmentTooltip 
            segment={segments.find(s => s.id === hoveredSegment)!} 
          />
        )}
      </AnimatePresence>
    </div>
  )
}

// Segment Path Component
function SegmentPath({ segment, isHovered, onMouseEnter, onMouseLeave, onClick }) {
  const startAngle = segment.angle
  const endAngle = segment.angle + 36
  
  const innerRadius = 60
  const outerRadius = 100
  
  // Calculate path
  const path = describeArc(100, 100, innerRadius, outerRadius, startAngle, endAngle)
  
  return (
    <motion.path
      d={path}
      fill={segment.color}
      opacity={isHovered ? 1 : 0.7}
      onMouseEnter={onMouseEnter}
      onMouseLeave={onMouseLeave}
      onClick={onClick}
      className="cursor-pointer transition-opacity"
      animate={{
        scale: isHovered ? 1.05 : 1,
      }}
      style={{ transformOrigin: '100px 100px' }}
    />
  )
}

// Helper: Calculate SVG arc path
function describeArc(x, y, innerRadius, outerRadius, startAngle, endAngle) {
  const start1 = polarToCartesian(x, y, outerRadius, endAngle)
  const end1 = polarToCartesian(x, y, outerRadius, startAngle)
  const start2 = polarToCartesian(x, y, innerRadius, endAngle)
  const end2 = polarToCartesian(x, y, innerRadius, startAngle)
  
  const largeArcFlag = endAngle - startAngle <= 180 ? '0' : '1'
  
  return [
    'M', start1.x, start1.y,
    'A', outerRadius, outerRadius, 0, largeArcFlag, 0, end1.x, end1.y,
    'L', end2.x, end2.y,
    'A', innerRadius, innerRadius, 0, largeArcFlag, 1, start2.x, start2.y,
    'Z'
  ].join(' ')
}

function polarToCartesian(centerX, centerY, radius, angleInDegrees) {
  const angleInRadians = (angleInDegrees - 90) * Math.PI / 180.0
  return {
    x: centerX + (radius * Math.cos(angleInRadians)),
    y: centerY + (radius * Math.sin(angleInRadians))
  }
}

// Tooltip Component
function SegmentTooltip({ segment }: { segment: Segment }) {
  return (
    <motion.div
      initial={{ opacity: 0, y: 10 }}
      animate={{ opacity: 1, y: 0 }}
      exit={{ opacity: 0, y: 10 }}
      className="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 bg-white rounded-lg shadow-xl p-4 min-w-[200px] z-10"
    >
      <h3 className="font-bold text-lg mb-2">{segment.name}</h3>
      <div className="flex items-center gap-2 mb-2">
        <div className="flex-1 bg-gray-200 rounded-full h-2">
          <motion.div
            className="h-full rounded-full"
            style={{ backgroundColor: segment.color }}
            initial={{ width: 0 }}
            animate={{ width: `${segment.progress}%` }}
          />
        </div>
        <span className="text-sm font-semibold">{segment.progress}%</span>
      </div>
      <button className="text-sm text-indigo-600 hover:text-indigo-800 font-medium">
        → Узнать больше
      </button>
    </motion.div>
  )
}
```

---

## 2. MOOD TRACKER (График волны)

```tsx
// components/interactive/MoodTracker.tsx
'use client'

import { useState } from 'react'
import { LineChart, Line, XAxis, YAxis, Tooltip, ResponsiveContainer } from 'recharts'

const MOOD_OPTIONS = [
  { value: 5, emoji: '😄', label: 'Отлично' },
  { value: 4, emoji: '😊', label: 'Хорошо' },
  { value: 3, emoji: '😐', label: 'Нормально' },
  { value: 2, emoji: '😔', label: 'Плохо' },
  { value: 1, emoji: '😢', label: 'Ужасно' },
]

export function MoodTracker({ data, onAddEntry }) {
  const [selectedMood, setSelectedMood] = useState<number | null>(null)
  const [note, setNote] = useState('')
  
  const handleSubmit = () => {
    if (!selectedMood) return
    
    onAddEntry({
      mood: selectedMood,
      note,
      date: new Date().toISOString()
    })
    
    setSelectedMood(null)
    setNote('')
  }
  
  return (
    <div className="space-y-8">
      {/* Input Section */}
      <div className="bg-white rounded-xl p-6 shadow-lg">
        <h2 className="text-2xl font-bold mb-4">Как ты сегодня?</h2>
        
        <div className="flex justify-center gap-4 mb-6">
          {MOOD_OPTIONS.map((option) => (
            <button
              key={option.value}
              onClick={() => setSelectedMood(option.value)}
              className={`
                flex flex-col items-center p-4 rounded-xl transition-all
                ${selectedMood === option.value 
                  ? 'bg-indigo-100 ring-2 ring-indigo-500 scale-110' 
                  : 'bg-gray-50 hover:bg-gray-100'
                }
              `}
            >
              <span className="text-4xl mb-2">{option.emoji}</span>
              <span className="text-sm font-medium">{option.value}</span>
            </button>
          ))}
        </div>
        
        <textarea
          value={note}
          onChange={(e) => setNote(e.target.value)}
          placeholder="Что помогло/мешало сегодня?"
          className="w-full p-4 border rounded-lg resize-none focus:ring-2 focus:ring-indigo-500 focus:border-transparent"
          rows={3}
        />
        
        <button
          onClick={handleSubmit}
          disabled={!selectedMood}
          className="mt-4 w-full bg-indigo-600 text-white py-3 rounded-lg font-semibold hover:bg-indigo-700 disabled:bg-gray-300 disabled:cursor-not-allowed"
        >
          Сохранить
        </button>
      </div>
      
      {/* Chart Section */}
      <div className="bg-white rounded-xl p-6 shadow-lg">
        <h2 className="text-2xl font-bold mb-4">Твоя волна за последний месяц</h2>
        
        <ResponsiveContainer width="100%" height={300}>
          <LineChart data={data}>
            <XAxis 
              dataKey="date" 
              tickFormatter={(date) => new Date(date).getDate().toString()}
            />
            <YAxis domain={[1, 5]} ticks={[1, 2, 3, 4, 5]} />
            <Tooltip 
              content={<CustomTooltip />}
            />
            <Line 
              type="monotone" 
              dataKey="mood" 
              stroke="#6366f1" 
              strokeWidth={3}
              dot={{ fill: '#6366f1', r: 6 }}
              activeDot={{ r: 8 }}
            />
          </LineChart>
        </ResponsiveContainer>
        
        {/* Statistics */}
        <div className="mt-6 grid grid-cols-3 gap-4">
          <StatCard
            label="Средний уровень"
            value={calculateAverage(data).toFixed(1)}
          />
          <StatCard
            label="Тренд"
            value={calculateTrend(data)}
            icon={getTrendIcon(calculateTrend(data))}
          />
          <StatCard
            label="Лучших дней"
            value={countGoodDays(data)}
          />
        </div>
      </div>
    </div>
  )
}

function CustomTooltip({ active, payload }) {
  if (!active || !payload?.length) return null
  
  const data = payload[0].payload
  const mood = MOOD_OPTIONS.find(m => m.value === data.mood)
  
  return (
    <div className="bg-white p-3 rounded-lg shadow-lg border">
      <p className="font-semibold">{new Date(data.date).toLocaleDateString('ru-RU')}</p>
      <p className="text-2xl my-2">{mood?.emoji}</p>
      {data.note && <p className="text-sm text-gray-600">{data.note}</p>}
    </div>
  )
}

function StatCard({ label, value, icon = null }) {
  return (
    <div className="bg-gray-50 p-4 rounded-lg text-center">
      <p className="text-sm text-gray-600 mb-1">{label}</p>
      <p className="text-2xl font-bold text-indigo-600">
        {icon} {value}
      </p>
    </div>
  )
}

// Helper functions
function calculateAverage(data) {
  return data.reduce((acc, d) => acc + d.mood, 0) / data.length
}

function calculateTrend(data) {
  const recent = data.slice(-7)
  const earlier = data.slice(-14, -7)
  const recentAvg = calculateAverage(recent)
  const earlierAvg = calculateAverage(earlier)
  
  if (recentAvg > earlierAvg + 0.3) return 'Растёт'
  if (recentAvg < earlierAvg - 0.3) return 'Падает'
  return 'Стабильно'
}

function getTrendIcon(trend) {
  if (trend === 'Растёт') return '↗'
  if (trend === 'Падает') return '↘'
  return '→'
}

function countGoodDays(data) {
  return data.filter(d => d.mood >= 4).length
}
```

---

## 3. ИНТЕРАКТИВНЫЙ ЧЕК-ЛИСТ

```tsx
// components/interactive/InteractiveChecklist.tsx
'use client'

import { useState, useEffect } from 'react'
import { motion, AnimatePresence } from 'framer-motion'
import confetti from 'canvas-confetti'

interface ChecklistItem {
  id: string
  text: string
  category: string
  completed: boolean
  tooltip?: string
}

interface ChecklistProps {
  title: string
  items: ChecklistItem[]
  onSave: (completed: string[]) => void
}

export function InteractiveChecklist({ title, items: initialItems, onSave }: ChecklistProps) {
  const [items, setItems] = useState(initialItems)
  const [showTooltip, setShowTooltip] = useState<string | null>(null)
  
  const completedCount = items.filter(i => i.completed).length
  const totalCount = items.length
  const progress = Math.round((completedCount / totalCount) * 100)
  
  const handleToggle = (id: string) => {
    setItems(prev => prev.map(item => 
      item.id === id ? { ...item, completed: !item.completed } : item
    ))
  }
  
  useEffect(() => {
    // Celebrate on 100% completion
    if (completedCount === totalCount && totalCount > 0) {
      confetti({
        particleCount: 100,
        spread: 70,
        origin: { y: 0.6 }
      })
    }
  }, [completedCount, totalCount])
  
  const handleSave = () => {
    const completedIds = items.filter(i => i.completed).map(i => i.id)
    onSave(completedIds)
  }
  
  // Group items by category
  const groupedItems = items.reduce((acc, item) => {
    if (!acc[item.category]) acc[item.category] = []
    acc[item.category].push(item)
    return acc
  }, {} as Record<string, ChecklistItem[]>)
  
  return (
    <div className="bg-white rounded-xl p-6 shadow-lg">
      {/* Header */}
      <div className="mb-6">
        <h2 className="text-2xl font-bold mb-2">{title}</h2>
        <div className="flex items-center gap-3">
          <div className="flex-1 bg-gray-200 rounded-full h-3">
            <motion.div
              className="bg-gradient-to-r from-indigo-500 to-purple-500 h-full rounded-full"
              initial={{ width: 0 }}
              animate={{ width: `${progress}%` }}
              transition={{ duration: 0.5, ease: 'easeOut' }}
            />
          </div>
          <span className="text-sm font-semibold text-gray-600">
            {completedCount}/{totalCount}
          </span>
        </div>
      </div>
      
      {/* Checklist Items */}
      <div className="space-y-6">
        {Object.entries(groupedItems).map(([category, categoryItems]) => (
          <div key={category}>
            <h3 className="font-bold text-lg mb-3 text-gray-700">{category}</h3>
            <div className="space-y-2">
              {categoryItems.map((item) => (
                <ChecklistItemComponent
                  key={item.id}
                  item={item}
                  onToggle={handleToggle}
                  showTooltip={showTooltip === item.id}
                  onTooltipToggle={() => setShowTooltip(
                    showTooltip === item.id ? null : item.id
                  )}
                />
              ))}
            </div>
          </div>
        ))}
      </div>
      
      {/* Save Button */}
      <button
        onClick={handleSave}
        className="mt-6 w-full bg-indigo-600 text-white py-3 rounded-lg font-semibold hover:bg-indigo-700 transition-colors"
      >
        Сохранить прогресс
      </button>
      
      {/* Completion Message */}
      <AnimatePresence>
        {progress === 100 && (
          <motion.div
            initial={{ opacity: 0, y: 20 }}
            animate={{ opacity: 1, y: 0 }}
            exit={{ opacity: 0 }}
            className="mt-4 p-4 bg-green-100 text-green-800 rounded-lg text-center font-semibold"
          >
            🎉 Отличная работа! Ты завершил(а) весь чек-лист!
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  )
}

function ChecklistItemComponent({ item, onToggle, showTooltip, onTooltipToggle }) {
  return (
    <div className="relative">
      <motion.div
        className={`
          flex items-start gap-3 p-3 rounded-lg transition-all cursor-pointer
          ${item.completed ? 'bg-green-50' : 'bg-gray-50 hover:bg-gray-100'}
        `}
        whileTap={{ scale: 0.98 }}
        onClick={() => onToggle(item.id)}
      >
        {/* Checkbox */}
        <div className="mt-1">
          <motion.div
            className={`
              w-6 h-6 rounded border-2 flex items-center justify-center
              ${item.completed 
                ? 'bg-green-500 border-green-500' 
                : 'bg-white border-gray-300'
              }
            `}
            animate={{
              scale: item.completed ? [1, 1.2, 1] : 1
            }}
          >
            {item.completed && (
              <motion.svg
                initial={{ pathLength: 0 }}
                animate={{ pathLength: 1 }}
                className="w-4 h-4 text-white"
                viewBox="0 0 20 20"
                fill="none"
              >
                <path
                  d="M6 10l2 2 6-6"
                  stroke="currentColor"
                  strokeWidth="2"
                  strokeLinecap="round"
                  strokeLinejoin="round"
                />
              </motion.svg>
            )}
          </motion.div>
        </div>
        
        {/* Text */}
        <div className="flex-1">
          <p className={`
            ${item.completed ? 'line-through text-gray-500' : 'text-gray-900'}
          `}>
            {item.text}
          </p>
        </div>
        
        {/* Info Icon */}
        {item.tooltip && (
          <button
            onClick={(e) => {
              e.stopPropagation()
              onTooltipToggle()
            }}
            className="text-gray-400 hover:text-gray-600"
          >
            <svg className="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
          </button>
        )}
      </motion.div>
      
      {/* Tooltip */}
      <AnimatePresence>
        {showTooltip && item.tooltip && (
          <motion.div
            initial={{ opacity: 0, y: -10 }}
            animate={{ opacity: 1, y: 0 }}
            exit={{ opacity: 0, y: -10 }}
            className="absolute z-10 mt-2 p-3 bg-gray-900 text-white text-sm rounded-lg shadow-lg max-w-xs"
          >
            {item.tooltip}
          </motion.div>
        )}
      </AnimatePresence>
    </div>
  )
}
```

---

## 4. PROGRESS DASHBOARD

```tsx
// components/dashboard/ProgressDashboard.tsx
'use client'

import { useMemo } from 'react'
import { CircularProgressbar, buildStyles } from 'react-circular-progressbar'
import 'react-circular-progressbar/dist/styles.css'

interface ProgressData {
  foundation: { sleep: number; nutrition: number; movement: number }
  heart: { partner: number; family: number; tribe: number }
  mind: { traps: number; priorities: number; digital: number }
  purpose: { mission: number; money: number; legacy: number }
}

export function ProgressDashboard({ data }: { data: ProgressData }) {
  const moduleAverages = useMemo(() => ({
    foundation: calculateAverage(Object.values(data.foundation)),
    heart: calculateAverage(Object.values(data.heart)),
    mind: calculateAverage(Object.values(data.mind)),
    purpose: calculateAverage(Object.values(data.purpose)),
  }), [data])
  
  const overallProgress = calculateAverage(Object.values(moduleAverages))
  
  return (
    <div className="space-y-8">
      {/* Overall Progress */}
      <div className="bg-white rounded-xl p-6 shadow-lg">
        <h2 className="text-2xl font-bold mb-6">Общий прогресс</h2>
        <div className="flex items-center gap-8">
          <div className="w-32 h-32">
            <CircularProgressbar
              value={overallProgress}
              text={`${Math.round(overallProgress)}%`}
              styles={buildStyles({
                textColor: '#6366f1',
                pathColor: '#6366f1',
                trailColor: '#e5e7eb',
              })}
            />
          </div>
          <div className="flex-1">
            <p className="text-gray-600 mb-2">
              {getMotivationalMessage(overallProgress)}
            </p>
            <div className="flex gap-2">
              {getProgressBadges(overallProgress).map((badge, i) => (
                <span 
                  key={i}
                  className="px-3 py-1 bg-indigo-100 text-indigo-700 rounded-full text-sm font-medium"
                >
                  {badge}
                </span>
              ))}
            </div>
          </div>
        </div>
      </div>
      
      {/* Module Progress */}
      <div className="grid md:grid-cols-2 gap-6">
        <ModuleCard
          title="Фундамент"
          color="#3b82f6"
          progress={moduleAverages.foundation}
          items={[
            { label: 'Сон', value: data.foundation.sleep },
            { label: 'Питание', value: data.foundation.nutrition },
            { label: 'Движение', value: data.foundation.movement },
          ]}
        />
        
        <ModuleCard
          title="Сердце"
          color="#ec4899"
          progress={moduleAverages.heart}
          items={[
            { label: 'Партнёр', value: data.heart.partner },
            { label: 'Род', value: data.heart.family },
            { label: 'Стая', value: data.heart.tribe },
          ]}
        />
        
        <ModuleCard
          title="Разум"
          color="#8b5cf6"
          progress={moduleAverages.mind}
          items={[
            { label: 'Ловушки', value: data.mind.traps },
            { label: 'Приоритеты', value: data.mind.priorities },
            { label: 'Цифр. гигиена', value: data.mind.digital },
          ]}
        />
        
        <ModuleCard
          title="Дело"
          color="#f59e0b"
          progress={moduleAverages.purpose}
          items={[
            { label: 'Миссия', value: data.purpose.mission },
            { label: 'Деньги', value: data.purpose.money },
            { label: 'Наследие', value: data.purpose.legacy },
          ]}
        />
      </div>
    </div>
  )
}

function ModuleCard({ title, color, progress, items }) {
  return (
    <div className="bg-white rounded-xl p-6 shadow-lg">
      <div className="flex items-center justify-between mb-4">
        <h3 className="text-xl font-bold">{title}</h3>
        <span className="text-2xl font-bold" style={{ color }}>
          {Math.round(progress)}%
        </span>
      </div>
      
      <div className="mb-4">
        <div className="w-full bg-gray-200 rounded-full h-2">
          <div
            className="h-full rounded-full transition-all duration-500"
            style={{ 
              width: `${progress}%`,
              backgroundColor: color
            }}
          />
        </div>
      </div>
      
      <div className="space-y-2">
        {items.map((item, i) => (
          <div key={i} className="flex items-center justify-between text-sm">
            <span className="text-gray-600">
              {getStatusIcon(item.value)} {item.label}
            </span>
            <span className="font-semibold">{item.value}%</span>
          </div>
        ))}
      </div>
    </div>
  )
}

// Helper functions
function calculateAverage(values: number[]) {
  return values.reduce((a, b) => a + b, 0) / values.length
}

function getStatusIcon(value: number) {
  if (value >= 70) return '✓'
  if (value >= 30) return '◐'
  return '○'
}

function getMotivationalMessage(progress: number) {
  if (progress >= 80) return 'Отличная работа! Ты на правильном пути.'
  if (progress >= 60) return 'Хороший прогресс! Продолжай в том же духе.'
  if (progress >= 40) return 'Движение есть. Не останавливайся!'
  if (progress >= 20) return 'Начало положено. Главное — регулярность.'
  return 'Каждый путь начинается с первого шага. Начни сегодня!'
}

function getProgressBadges(progress: number) {
  const badges = []
  if (progress >= 25) badges.push('🌱 Новичок')
  if (progress >= 50) badges.push('🏃 В пути')
  if (progress >= 75) badges.push('⭐ Мастер')
  if (progress >= 90) badges.push('🏆 Чемпион')
  return badges
}
```

---

