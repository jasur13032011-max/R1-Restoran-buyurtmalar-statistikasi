# R1-Restoran-buyurtmalar-statistikasi
const orders = [
  { id: 1, waiter: "Ali", dish: "Osh", price: 45000, date: "2026-06-12", category: "Milliy" },
  { id: 2, waiter: "Zuhra", dish: "Pizza", price: 85000, date: "2026-06-12", category: "Yevropa" },
  { id: 3, waiter: "Olim", dish: "Shashlik", price: 35000, date: "2026-06-13", category: "Milliy" },
  { id: 4, waiter: "Ali", dish: "Manti", price: 40000, date: "2026-06-12", category: "Milliy" },
  { id: 5, waiter: "Zuhra", dish: "Burger", price: 38000, date: "2026-06-13", category: "Fast Food" },
  { id: 6, waiter: "Olim", dish: "Lag'mon", price: 42000, date: "2026-06-12", category: "Milliy" },
  { id: 7, waiter: "Ali", dish: "Steyk", price: 120000, date: "2026-06-14", category: "Yevropa" },
  { id: 8, waiter: "Madina", dish: "Sezar salat", price: 50000, date: "2026-06-12", category: "Salatlar" },
  { id: 9, waiter: "Zuhra", dish: "Fri", price: 20000, date: "2026-06-14", category: "Fast Food" },
  { id: 10, waiter: "Olim", dish: "Chuchvara", price: 38000, date: "2026-06-14", category: "Milliy" },
  { id: 11, waiter: "Madina", dish: "Hot-dog", price: 22000, date: "2026-06-13", category: "Fast Food" },
  { id: 12, waiter: "Ali", dish: "Somsi", price: 30000, date: "2026-06-13", category: "Milliy" },
  { id: 13, waiter: "Zuhra", dish: "Pasta", price: 65000, date: "2026-06-13", category: "Yevropa" },
  { id: 14, waiter: "Olim", dish: "Grek salat", price: 35000, date: "2026-06-12", category: "Salatlar" },
  { id: 15, waiter: "Madina", dish: "Xanim", price: 32000, date: "2026-06-14", category: "Milliy" }
];

// Pullarni so'm formatiga o'tkazish uchun helper
const formatMoney = amount => amount.toLocaleString('uz-UZ', { style: 'currency', currency: 'UZS' });
// Set yordamida takrorlanmas kategoriyalar ro'yxatini olish
const uniqueCategories = [...new Set(orders.map(({ category }) => category))];

console.log("=== UNIQUE KATEGORIYALAR ===");
console.log(uniqueCategories.join(", ")); 
// Natija: Milliy, Yevropa, Fast Food, Salatlar
const groupedData = orders.reduce((acc, { waiter, category, date, price }) => {
  // Ofitsiant bo'yicha guruhlash va jami summasini hisoblash
  acc.byWaiter[waiter] = (acc.byWaiter[waiter] || 0) + price;

  // Kategoriya bo'yicha guruhlash
  acc.byCategory[category] = (acc.byCategory[category] || 0) + price;

  // Sana bo'yicha guruhlash
  acc.byDate[date] = (acc.byDate[date] || 0) + price;

  return acc;
}, { byWaiter: {}, byCategory: {}, byDate: {} });
const top3Waiters = Object.entries(groupedData.byWaiter)
  .sort((a, b) => b[1] - a[1]) // Kamayish tartibida saralash (Lambda)
  .slice(0, 3)                 // TOP 3 tasini kesib olish
  .map(([name, total]) => ({ name, total }));

console.log("\n=== TOP 3 OFITSIANT ===");
top3Waiters.forEach(({ name, total }, index) => {
  console.log(`${index + 1}. ${name} -> Jami savdo: ${formatMoney(total)}`);
});
const generateTableReport = (dataList) => {
  const header = `${"ID".padEnd(6)}${"Ofitsiant".padEnd(15)}${"Taom".padEnd(15)}${"Kategoriya".padEnd(15)}${"Sana".padEnd(15)}${"Narx".padStart(15)}`;
  const line = "-".repeat(81);

  const rows = dataList.map(({ id, waiter, dish, category, date, price }) => {
    // Har bir satr uchun qiymatlarni template literal va pad metodlari bilan tekislash
    return `${String(id).padEnd(6)}${waiter.padEnd(15)}${dish.padEnd(15)}${category.padEnd(15)}${date.padEnd(15)}${formatMoney(price).padStart(15)}`;
  }).join("\n");

  // Ko'p qatorli template literal orqali yakuniy jadval
  return `
================================= BUYURTMALAR JADVALI =================================
${header}
${line}
${rows}
${line}
Jami buyurtmalar soni: ${dataList.length} ta
=======================================================================================
`;
};

console.log(generateTableReport(orders));
console.log("\n=== KATEGORIYALAR BO'YICHA SAVDO ===");
Object.entries(groupedData.byCategory).forEach(([cat, sum]) => console.log(`${cat.padEnd(12)}: ${formatMoney(sum)}`));

console.log("\n=== KUNLIK SANOQ (SANA BO'YICHA) ===");
Object.entries(groupedData.byDate).forEach(([date, sum]) => console.log(`${date} : ${formatMoney(sum)}`));
