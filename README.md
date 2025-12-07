# تسک استخدام توسعه‌دهنده فرانت‌اند - طنین کارت

## 📋 درباره تسک

این تسک برای ارزیابی مهارت‌های فنی شما در توسعه فرانت‌اند با Next.js طراحی شده است. شما باید یک فروشگاه آنلاین ساده با داشبورد مدیریت بسازید که با Backend ما ارتباط برقرار کند.

---

## 🎯 الزامات فنی

### استک اصلی
- **Next.js 14+** با App Router
- **TypeScript** (الزامی)
- **Styling**: Tailwind CSS (الزامی)

### کتابخانه‌های الزامی
- **TanStack Query (React Query)** - برای مدیریت server state
- **Axios** - برای فراخوانی API
- **Shadcn/ui** - برای کامپوننت‌های پایه
- **Material-UI (MUI)** - می‌توانید در کنار Shadcn استفاده کنید
- **React Hook Form** - برای مدیریت فرم‌ها

### الزامات کلی
- طراحی کاملاً **Responsive** (موبایل، تبلت، دسکتاپ)
- مدیریت صحیح خطاها و loading states
- کد تمیز و مستندسازی شده
- معماری قابل توسعه

---

## 🛍️ بخش 1: فروشگاه عمومی (SSR)

### صفحه لیست محصولات - `/products`

**نوع Rendering:** SSR (Server-Side Rendering) - بدون SSG/ISR

**الزامات:**
- Fetch محصولات از Backend در سمت سرور
- نمایش محصولات به صورت Grid یا List شامل:
  - تصویر محصول
  - نام محصول
  - قیمت (با نمایش واحد پولی مناسب)
  - وضعیت موجودی
  
**ویژگی‌های اضافی:**
- فیلتر براساس دسته‌بندی (Categories)
- فیلتر براساس برند (Brands)
- جستجو براساس نام محصول
- محدوده قیمت (min_price و max_price)
- Pagination

**مثال URL:**
```
/products
/products?categories[]=electronics&search=laptop
/products?min_price=100000&max_price=500000
/products?page=2&per_page=20
```

---

### صفحه جزئیات محصول - `/products/[slug]`

**نوع Rendering:** SSR - در هر درخواست

**الزامات:**
- Fetch جزئیات محصول از Backend در سمت سرور با استفاده از slug
- نمایش اطلاعات کامل محصول:
  - نام محصول
  - توضیحات کامل (description و excerpt)
  - تصاویر (با امکان preview/gallery)
  - قیمت
  - وضعیت موجودی
  - دسته‌بندی‌ها و برندها
  - Properties (ویژگی‌های محصول)
  - Variants (اگر موجود است - نمایش گزینه‌های مختلف با قیمت و موجودی)
  
- اگر محصول پیدا نشد (404)، صفحه 404 نمایش داده شود
- دکمه "افزودن به سبد خرید"
- نمایش نظرات (Comments) و امتیاز میانگین (Average Rating)

**مدیریت سبد خرید:**
- سبد خرید Client-side باشد (بدون نیاز به Backend)
- از Context API، Zustand یا هر state management استفاده کنید
- نمایش تعداد آیتم‌های سبد در Header/Navbar
- امکان انتخاب Variant قبل از افزودن به سبد

---

## 🎛️ بخش 2: داشبورد مدیریت (SSG)

### صفحه محصولات داشبورد - `/dashboard/products`

**نوع Rendering:** (Static Site Generation) 

**الزامات:**
- استفاده از ISR با مقدار revalidate (60 ثانیه)
- نمایش محصولات در قالب Table شامل:
  - تصویر محصول (thumbnail)
  - نام محصول
  - Slug محصول
  - قیمت پایه
  - وضعیت (Status: publish, draft, archive)
  - دسته‌بندی‌ها
  - تاریخ ایجاد

**ویژگی‌های اضافی:**
- جستجوی Client-side براساس نام محصول
- مرتب‌سازی ستون‌ها
- Pagination
- فیلتر براساس وضعیت (Status)
- فیلتر براساس دسته‌بندی


---

## 🔌 Backend API Endpoints

Base URL شما: `https://shop-api-beta.tanincard.ir`

**⚠️ توجه:** آدرس پایه Backend را در فایل `.env.local` قرار دهید

```bash
# .env.local
NEXT_PUBLIC_API_BASE_URL=https://shop-api-beta.tanincard.ir
```

### Endpoints مورد نیاز:

#### 1. دریافت لیست محصولات (Public)
```
GET /api/v1/product
Query Params:
  - search: string (جستجو در نام محصول)
  - categories[]: array of strings (slug های دسته‌بندی)
  - brands[]: array of strings (slug های برند)
  - product_type[]: array of strings (slug های نوع محصول)
  - min_price: integer (حداقل قیمت)
  - max_price: integer (حداکثر قیمت)
  - order_by: string (max_price, min_price, newest, most_viewed, most_sells)
  - page: integer (شماره صفحه)
  - per_page: integer (تعداد در هر صفحه)
```

**نمونه Response:**
```json
{
  "data": [
    {
      "name": "ماوس بی‌سیم",
      "slug": "wireless-mouse-001",
      "excerpt": "ماوس بی‌سیم ارگونومیک",
      "type": "electronics",
      "status": "publish",
      "description": "توضیحات کامل...",
      "categories": [...],
      "brands": [...],
      "properties": [...],
      "media": {
        "path": "/path/to/image.jpg",
        "url": "https://..."
      },
      "average_rating": 4.5,
      "review_count": 23,
      "created_at": "2024-01-15T10:30:00Z"
    }
  ],
  "meta": {
    "current_page": 1,
    "total": 100,
    "per_page": 20
  }
}
```

#### 2. دریافت جزئیات یک محصول (Public)
```
GET /api/v1/product/{slug}
```

**نمونه Response:** همانند بالا اما یک محصول

#### 3. دریافت Product Facets (برای فیلترها)
```
GET /api/v1/product/facets
Query Params: (همانند endpoint لیست محصولات)
```

این endpoint اطلاعات فیلترهای موجود را برمی‌گرداند (دسته‌بندی‌ها، برندها، محدوده قیمت و...)

#### 4. دریافت لیست دسته‌بندی‌ها
```
GET /api/v1/category
Query Params:
  - filter[type]: string (product, brand, product_type, faq)
  - filter[search]: string (جستجو در نام دسته‌بندی)
```

**نمونه Response:**
```json
{
  "data": [
    {
      "name": "الکترونیک",
      "slug": "electronics",
      "status": "publish",
      "type": "product",
      "description": "محصولات الکترونیکی",
      "children": [...],
      "media": {
        "path": "/path/to/image.jpg",
        "url": "https://..."
      }
    }
  ]
}
```

#### 5. دریافت جزئیات یک دسته‌بندی
```
GET /api/v1/category/{slug}
```

---

## 📦 ساختار پروژه پیشنهادی

```
├── app/
│   ├── products/
│   │   ├── page.tsx              # لیست محصولات (SSR)
│   │   └── [slug]/
│   │       └── page.tsx          # جزئیات محصول (SSR)
│   ├── dashboard/
│   │   └── products/
│   │       └── page.tsx          # داشبورد محصولات (SSG)
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── ProductCard.tsx
│   ├── ProductFilter.tsx
│   ├── ProductTable.tsx
│   └── Cart/
│       ├── CartContext.tsx
│       └── CartButton.tsx
├── lib/
│   ├── api.ts                    # API client (Axios)
│   ├── queryClient.ts            # React Query setup
│   └── types.ts                  # TypeScript types
├── hooks/
│   ├── useProducts.ts
│   └── useCart.ts
├── .env.local                    # Environment variables
└── README.md
```

---

## 🚀 نحوه اجرا

### 1. Clone و Setup

```bash
# Fork کردن ریپو
# سپس Clone کنید:
git clone https://github.com/YOUR_USERNAME/frontend.git
cd frontend

# نصب Dependencies
npm install
# یا
yarn install
# یا
pnpm install
```

### 2. تنظیم Environment Variables

فایل `.env.local` بسازید:

```bash
NEXT_PUBLIC_API_BASE_URL=https://shop-api-beta.tanincard.ir
```

### 3. اجرای پروژه

```bash
# Development mode
npm run dev

# Build
npm run build

# Production mode
npm start
```

پروژه روی `http://localhost:3000` اجرا می‌شود.

---

## 📤 نحوه تحویل

### مراحل:

1. **Fork** کردن این ریپو: `https://github.com/tanincard/frontend`
2. پیاده‌سازی تسک در ریپوی Fork شده خود
3. ارسال لینک ریپو به ایمیل زیر:

**ایمیل:** mr.rezakarimi99@gmail.com  
**موضوع:** "ارسال تسک فرانت‌اند - [نام شما]"

### در ایمیل خود شامل کنید:

- لینک ریپوی GitHub
- لینک دمو زنده (اگر Deploy کردید - اختیاری اما توصیه می‌شود)
- README.md خود را Update کنید با:
  - نحوه اجرای پروژه
  - Endpoint هایی که استفاده کردید
  - Trade-off ها یا بخش‌های ناقص (در صورت وجود)
  - زمان صرف شده برای تسک
  - چالش‌های پیش رو و نحوه حل آن‌ها

### گیت کامیت‌ها:

- از commit های معنادار استفاده کنید
- تاریخچه git باید تمیز و خوانا باشد
- مثال:
  - `feat: add product list page with SSR`
  - `feat: implement product filters`
  - `fix: handle 404 for product not found`
  - `refactor: optimize product card component`

---

## 🎯 معیارهای ارزیابی

### 1. کیفیت کد (40%)
- کد تمیز و خوانا
- استفاده صحیح از TypeScript
- معماری و ساختار پروژه
- کامپوننت‌های قابل استفاده مجدد
- Error handling مناسب

### 2. عملکرد و بهینه‌سازی (25%)
- استفاده صحیح از SSR/SSG
- مدیریت State با React Query
- بهینه‌سازی تصاویر (Next.js Image)
- Code splitting
- Performance optimization

### 3. UI/UX (20%)
- طراحی Responsive و زیبا
- تجربه کاربری روان
- مدیریت Loading و Error states
- Accessibility (دسترسی‌پذیری)

### 4. الزامات فنی (15%)
- استفاده از تکنولوژی‌های خواسته شده
- پیاده‌سازی صحیح ویژگی‌ها
- Integration صحیح با Backend API

---

## ✅ چک‌لیست

قبل از ارسال، مطمئن شوید:

### صفحات:
- [ ] صفحه `/products` با SSR کار می‌کند
- [ ] فیلترها و جستجو در صفحه محصولات کار می‌کند
- [ ] Pagination پیاده‌سازی شده
- [ ] صفحه `/products/[slug]` با SSR کار می‌کند
- [ ] صفحه 404 برای محصول پیدا نشده نمایش داده می‌شود
- [ ] صفحه `/dashboard/products` با SSG کار می‌کند

### ویژگی‌ها:
- [ ] سبد خرید (client-side) پیاده‌سازی شده
- [ ] نمایش تعداد آیتم‌های سبد در Header
- [ ] مدیریت Variants در صفحه جزئیات محصول

### فنی:
- [ ] Next.js 14+ با App Router استفاده شده
- [ ] TypeScript در همه جا استفاده شده
- [ ] React Query برای data fetching
- [ ] Axios برای API calls
- [ ] Tailwind CSS برای styling
- [ ] Shadcn/ui و/یا MUI برای components

### کیفیت:
- [ ] طراحی Responsive است (تست شده روی موبایل، تبلت، دسکتاپ)
- [ ] مدیریت خطا و loading states
- [ ] کد تمیز و مستندسازی شده
- [ ] README.md به‌روزرسانی شده با جزئیات
- [ ] فایل `.env.example` برای نمونه وجود دارد
- [ ] Commit های معنادار

---

## 💡 نکات مهم

### آنچه مهم است:
✅ **کیفیت کد** بیش از تعداد ویژگی‌ها  
✅ **استفاده صحیح از SSR/SSG** - این خیلی مهم است!  
✅ **Type Safety** با TypeScript  
✅ **Performance** و بهینه‌سازی  
✅ **Clean Architecture** و قابلیت نگهداری  

### آنچه مهم نیست:
❌ طراحی بسیار پیچیده (ساده، تمیز و کاربردی کافی است)  
❌ انیمیشن‌های پیچیده  
❌ ویژگی‌های اضافی که خواسته نشده  

### مجاز است:
✅ استفاده از کتابخانه‌های اضافی (با توجیه)  
✅ استفاده از AI tools برای کمک (اما باید کد را درک کنید)  
✅ پرسیدن سوال از طریق Issues یا ایمیل  

### مجاز نیست:
❌ کپی کردن راه‌حل‌های کامل از منابع دیگر  
❌ تحویل کاری که متعلق به شما نیست  

---

## ❓ سوال دارید؟

- **ایمیل:** mr.rezakarimi99@gmail.com
- **GitHub Issues:** از طریق Issues این ریپو سوال بپرسید
- زمان پاسخ: ظرف 24 ساعت

---

## ⏰ زمان‌بندی

- **زمان تخمینی:** 1-2 روز
- **مهلت تحویل:** [در ایمیل ارسالی به شما اعلام می‌شود]
- **بررسی:** 2-3 روز کاری
- **مصاحبه:** در صورت تأیید، برای بحث درباره کد شما

---

## 🎁 نکات اضافی برای موفقیت

### 1. TypeScript Types
برای Response های API، Type های مناسب تعریف کنید:

```typescript
// lib/types.ts
export interface Product {
  name: string;
  slug: string;
  excerpt: string;
  type: string;
  status: 'publish' | 'draft' | 'archive';
  description: string;
  categories: Category[];
  brands: Category[];
  properties: Property[];
  media: Media;
  average_rating?: number;
  review_count?: number;
  created_at: string;
}

export interface Category {
  name: string;
  slug: string;
  status: string;
  type: string;
  description?: string;
  media?: Media;
}

export interface Media {
  path: string;
  url: string;
}
```

### 2. React Query Usage
```typescript
// hooks/useProducts.ts
import { useQuery } from '@tanstack/react-query';
import { fetchProducts } from '@/lib/api';

export const useProducts = (params: ProductParams) => {
  return useQuery({
    queryKey: ['products', params],
    queryFn: () => fetchProducts(params),
  });
};
```

### 3. SSR در Next.js
```typescript
// app/products/page.tsx
export default async function ProductsPage({
  searchParams,
}: {
  searchParams: { search?: string; page?: string }
}) {
  const products = await fetchProducts({
    search: searchParams.search,
    page: Number(searchParams.page) || 1,
  });

  return <ProductList products={products} />;
}
```

---

**موفق باشید! ما مشتاقانه منتظر دیدن کار شما هستیم! 🚀**

---

## 📚 منابع مفید

- [Next.js 14 Documentation](https://nextjs.org/docs)
- [App Router Guide](https://nextjs.org/docs/app)
- [TanStack Query](https://tanstack.com/query/latest)
- [Shadcn/ui](https://ui.shadcn.com/)
- [TypeScript Best Practices](https://www.typescriptlang.org/docs/)
- [Tailwind CSS](https://tailwindcss.com/docs)

---

**طنین کارت** - فروشگاه تخصصی محصولات ورزشی  
🌐 https://tanincard.ir  
📧 mr.rezakarimi99@gmail.com
