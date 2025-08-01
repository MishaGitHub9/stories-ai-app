# 🧪 Інструкції для тестування додатку

## ✅ Всі обмеження знято для тестування

### **Що змінено:**

1. **Ліміти історій:** `999,999` замість `3` на день
2. **Ліміти повідомлень:** `999,999` замість `20` на історію
3. **Функція `canPerformAction`:** Завжди повертає `true`
4. **Стани `canGenerateStory` та `canSendMessage`:** Завжди `true`
5. **Лічильники `storiesRemaining` та `messagesRemaining`:** Завжди `999,999`
6. **Функція перекладу:** Доступна для всіх користувачів (без підписки)

### **Як тестувати:**

#### **1. Тестування генерації історій:**
- Відкрийте **AI Mode** або **Story Mode**
- Виберіть будь-яку тему та рівень
- Генеруйте історії без обмежень
- Перевірте, що модальне вікно про ліміт не з'являється

#### **2. Тестування повідомлень в AI історіях:**
- Запустіть AI історію
- Відправляйте повідомлення без обмежень
- Перевірте, що ліміт повідомлень не досягається

#### **3. Тестування для анонімних користувачів:**
- Використовуйте додаток без реєстрації
- Генеруйте історії та відправляйте повідомлення
- Перевірте, що ліміти не застосовуються

#### **4. Тестування для зареєстрованих користувачів:**
- Увійдіть в акаунт
- Тестуйте всі функції без обмежень
- Перевірте, що підписка не потрібна

#### **5. Тестування функції перекладу:**
- Відкрийте AI історію
- Довго натисніть на будь-яке повідомлення
- Перевірте, що переклад працює для всіх мов
- Змініть мову перекладу в налаштуваннях
- Перевірте, що переклад доступний без підписки

### **Логи для відстеження:**

В консолі ви побачите повідомлення:
```
TESTING: Allowing action: generate_story
TESTING: Allowing action: send_message
```

#### **Логи вартості перекладу:**
```
🎭 Translation:
Input tokens: 45
Output tokens: 12
Cost: $0.000123
```

### **Перевірка в базі даних:**

Лічильники все ще оновлюються в Supabase:
- `stories_generated_today` - кількість згенерованих історій
- `messages_sent_today` - кількість відправлених повідомлень

### **Як повернути обмеження:**

Після тестування замініть в `hooks/useLimits.ts`:

```typescript
// Повернути оригінальні ліміти
const FREE_LIMITS = {
  STORIES_PER_DAY: 3,
  MESSAGES_PER_STORY_PER_DAY: 20,
} as const;

// Повернути оригінальну логіку canPerformAction
const canPerformAction = useCallback((action: 'generate_story' | 'send_message') => {
  switch (action) {
    case 'generate_story':
      return limits.canGenerateStory;
    case 'send_message':
      return limits.canSendMessage;
    default:
      return false;
  }
}, [limits.canGenerateStory, limits.canSendMessage]);
```

**Також в `app/ai-story.tsx` розкоментувати перевірку підписки для перекладу:**

```typescript
// Allow translation only for subscription
if (!limits.hasActiveSubscription) {
  setAlertTitle('Available with subscription only');
  setAlertMessage('Message translation is available only for users with subscription.');
  setAlertVisible(true);
  return;
}
```

### **Важливо:**

- ✅ **Лічильники працюють** - дані зберігаються в БД
- ✅ **Модальні вікна не з'являються** - обмеження знято
- ✅ **Всі функції доступні** - можна тестувати повністю
- ⚠️ **Не забудьте повернути обмеження** після тестування

### **Тестування завершено:**

Після тестування всіх функцій, поверніть оригінальні обмеження для продакшн версії. 