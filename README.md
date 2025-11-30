# AI-Based-multilingual-Digital-Farmer-Marketplace

## Overview

The AI-Based Multilingual Digital Farmer Marketplace is a smart platform designed to directly connect farmers with buyers by eliminating middlemen and improving transparency in agricultural trade. It integrates Artificial Intelligence for crop price prediction, Blockchain for secure transactions, and a Multilingual Chatbot to assist farmers in regional languages. The system ensures fair pricing, secure payments, and easy accessibility for rural farmers with low digital literacy.

## Problem Statement

Farmers in India face challenges such as:

Dependence on intermediaries resulting in low profits

Lack of real-time crop price information

Delayed or fraudulent payments

Limited digital and English literacy

No proper platform that combines AI insights, secure transactions, and multilingual support

There is a need for an intelligent, transparent, and easy-to-use digital marketplace that empowers farmers and ensures fair trade.

# Features
## AI-Based Crop Price Prediction
   
Forecasts future crop prices using ARIMA + N-HiTS models

Helps farmers decide the right time to sell

Uses historical, seasonal, and weather-based data

##  Blockchain-Based Secure Transactions

Tamper-proof transaction records

Smart-contract–based instant payments

Eliminates fraud and ensures transparency

## Multilingual AI Chatbot

Supports major Indian languages

Provides assistance through text/voice

Helps low-literate farmers use the platform easily

##  Direct Farmer-to-Buyer Marketplace

Upload crop products

Buyers can view, negotiate, and purchase directly

Higher profit for farmers, better quality for buyers

##  User Dashboards

Farmer dashboard (products, price prediction, transactions)

Buyer dashboard (browse, purchase, track orders)

# Requirements
## Hardware Requirements

Processor: Dual-core or higher

RAM: 4GB minimum

Hard Disk: 120GB

Input: Keyboard, Mouse

## Software Requirements

Operating System: Windows 10/11

Programming Languages: Python, JavaScript

Database: MySQL

Frameworks: React, Node.js

AI Tools: TensorFlow / PyTorch, Scikit-Learn

Blockchain: Smart Contracts or basic Blockchain module

# Program
```

App.py:


import { Switch, Route, Redirect } from "wouter";
import { queryClient } from "./lib/queryClient";
import { QueryClientProvider } from "@tanstack/react-query";
import { Toaster } from "@/components/ui/toaster";
import { TooltipProvider } from "@/components/ui/tooltip";
import { ThemeProvider } from "@/contexts/ThemeProvider";
import { AuthProvider, useAuth } from "@/contexts/AuthContext";
import { Header } from "@/components/Header";
import { Chatbot } from "@/components/Chatbot";
import NotFound from "@/pages/not-found";
import Home from "@/pages/Home";
import Login from "@/pages/Login";
import Register from "@/pages/Register";
import Marketplace from "@/pages/Marketplace";
import ProductDetail from "@/pages/ProductDetail";
import FarmerDashboard from "@/pages/FarmerDashboard";
import AddProduct from "@/pages/AddProduct";
import PricePredictor from "@/pages/PricePredictor";
import Transactions from "@/pages/Transactions";

function ProtectedRoute({ component: Component, requiredRole }: { component: any; requiredRole?: "farmer" | "buyer" }) {
  const { isAuthenticated, user } = useAuth();

  if (!isAuthenticated) {
    return <Redirect to="/login" />;
  }

  if (requiredRole && user?.role !== requiredRole) {
    return <Redirect to={user?.role === "farmer" ? "/farmer/dashboard" : "/marketplace"} />;
  }

  return <Component />;
}

function Router() {
  return (
    <Switch>
      <Route path="/" component={Home} />
      <Route path="/login" component={Login} />
      <Route path="/register" component={Register} />
      <Route path="/marketplace" component={Marketplace} />
      <Route path="/product/:id" component={ProductDetail} />
      <Route path="/transactions">
        {() => <ProtectedRoute component={Transactions} />}
      </Route>
      
      {/* Farmer Routes */}
      <Route path="/farmer/dashboard">
        {() => <ProtectedRoute component={FarmerDashboard} requiredRole="farmer" />}
      </Route>
      <Route path="/farmer/add-product">
        {() => <ProtectedRoute component={AddProduct} requiredRole="farmer" />}
      </Route>
      <Route path="/farmer/predict-price/:id">
        {() => <ProtectedRoute component={PricePredictor} requiredRole="farmer" />}
      </Route>

      {/* Fallback to 404 */}
      <Route component={NotFound} />
    </Switch>
  );
}

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <ThemeProvider>
        <AuthProvider>
          <TooltipProvider>
            <div className="min-h-screen bg-background">
              <Header />
              <Router />
              <Chatbot />
            </div>
            <Toaster />
          </TooltipProvider>
        </AuthProvider>
      </ThemeProvider>
    </QueryClientProvider>
  );
}



Index.html


<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1" />
    <title>FarmConnect - Agricultural Marketplace with AI Price Predictions</title>
    <meta name="description" content="Connect farmers directly with buyers. Browse fresh produce, get AI-powered price predictions, and support local agriculture. Multilingual support in English, Hindi, Tamil, and Telugu.">
    
    <!-- Open Graph / Social Media -->
    <meta property="og:type" content="website">
    <meta property="og:title" content="FarmConnect - Agricultural Marketplace">
    <meta property="og:description" content="Direct farm-to-buyer marketplace with AI price predictions and multilingual support">
    <meta property="og:image" content="/og-image.jpg">
    
    <!-- Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Poppins:wght@400;500;600;700;800&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>



Price Prediction . py


# ------------------------------------------------------
# EDA for a Particular Commodity Across Top 10 Markets
# ------------------------------------------------------
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load dataset
df = pd.read_excel(r"D:\Researchwork24-25\studentwork-25-26\dataset_new.xlsx")

# Standardize column names
#df.columns = [c.replace(" ", "_").replace("x0020_", "") for c in df.columns]

# Choose commodity
commodity_name = "Potato"   
# Filter for the selected commodity
df_com = df[df["Commodity"] == commodity_name]

print("Total records for", commodity_name, ":", len(df_com))
print("\nColumns available:")
print(df_com.columns)

# ------------------------------------------------------
# EDA 1: Top 10 Markets (Highest frequency)
# ------------------------------------------------------
top_markets = (
    df_com["Market"]
    .value_counts()
    .head(10)
)

print("\nTop 10 Markets for this commodity:")
print(top_markets)

# Filter dataset to include only top 10 markets
df_top = df_com[df_com["Market"].isin(top_markets.index)]

# ------------------------------------------------------
# EDA 2: Price analysis - Average Modal Price per Market
# ------------------------------------------------------
price_summary = (
    df_top.groupby("Market")["Modal_Price"]
    .mean()
    .sort_values(ascending=False)
    .head(10)
)

print("\nAverage Modal Price in Top 10 Markets:")
print(price_summary)

# ------------------------------------------------------
# BAR GRAPH – Top 10 markets average price
# ------------------------------------------------------
plt.figure(figsize=(12, 6))
sns.barplot(x=price_summary.index, y=price_summary.values)
plt.title(f"Average Modal Price of {commodity_name} in Top 10 Markets")
plt.xlabel("Market")
plt.ylabel("Average Modal Price")
plt.xticks(rotation=45)
plt.grid(axis="y", linestyle="--", alpha=0.4)
plt.tight_layout()
plt.show()

# ------------------------------------------------------
# ADDITIONAL EDA – Time Trend (Optional)
# ------------------------------------------------------
df_top["Arrival_Date"] = pd.to_datetime(df_top["Arrival_Date"], errors='coerce')

plt.figure(figsize=(14, 6))
sns.lineplot(data=df_top, x="Arrival_Date", y="Modal_Price", hue="Market")
plt.title(f"Price Trend of {commodity_name} across Top 10 Markets")
plt.xlabel("Date")
plt.ylabel("Modal Price")
plt.legend(title="Market", bbox_to_anchor=(1.03, 1), loc='upper left')
plt.grid(True)
plt.tight_layout()
plt.show()



#df = pd.read_excel(r"D:\Researchwork24-25-26\hemasonica\dataset_new.xlsx")
#df.columns = [c.replace(" ", "_").replace("x0020_", "") for c in df.columns]

# Average Modal Price per State
state_avg = df.groupby("State")["Modal_Price"].mean().sort_values(ascending=False)

plt.figure(figsize=(12,6))
sns.barplot(x=state_avg.index, y=state_avg.values)
plt.title("Average Modal Price per State")
plt.xlabel("State")
plt.ylabel("Average Modal Price")
plt.xticks(rotation=45)
plt.grid(axis='y', alpha=0.3)
plt.tight_layout()
plt.show()


Chatbot.tsx :


import { useState } from "react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { MessageSquare, Send, X, Loader2 } from "lucide-react";
import { apiRequest } from "@/lib/queryClient";
import { useToast } from "@/hooks/use-toast";

type Msg = { role: "user" | "assistant"; content: string };

const LANGS = {
  en: { n: "English", f: "🇬🇧" },
  hi: { n: "Hindi", f: "🇮🇳" },
  ta: { n: "Tamil", f: "🇮🇳" },
  te: { n: "Telugu", f: "🇮🇳" },
} as const;

export default function Chatbot() {
  const [open, setOpen] = useState(false);
  const [lang, setLang] = useState<keyof typeof LANGS>("en");
  const [msgs, setMsgs] = useState<Msg[]>([{ role: "assistant", content: "Hello! How can I help you today?" }]);
  const [text, setText] = useState("");
  const [loading, setLoading] = useState(false);
  const { toast } = useToast();

  const send = async () => {
    if (!text.trim() || loading) return;
    const m = text.trim();
    setText("");
    setMsgs((p) => [...p, { role: "user", content: m }]);
    setLoading(true);
    try {
      const res: any = await apiRequest("POST", "/api/chat", { message: m, language: lang });
      setMsgs((p) => [...p, { role: "assistant", content: res.response }]);
    } catch {
      toast({ title: "Chat error", description: "Failed to get response.", variant: "destructive" });
    } finally {
      setLoading(false);
    }
  };

  return (
    <>
      {!open && (
        <Button size="icon" className="fixed bottom-6 right-6 h-14 w-14 z-40" onClick={() => setOpen(true)} data-testid="button-open-chat">
          <MessageSquare className="h-6 w-6" />
        </Button>
      )}

      {open && (
        <Card className="fixed bottom-6 right-6 w-96 h-[500px] flex flex-col z-40">
          <CardHeader className="flex items-center justify-between pb-3 border-b">
            <CardTitle className="text-lg">AI Assistant</CardTitle>
            <div className="flex items-center gap-2">
              <Select value={lang} onValueChange={(v: any) => setLang(v)}>
                <SelectTrigger className="w-32 h-8" data-testid="select-language"><SelectValue /></SelectTrigger>
                <SelectContent>
                  {Object.entries(LANGS).map(([k, v]) => <SelectItem key={k} value={k}>{v.f} {v.n}</SelectItem>)}
                </SelectContent>
              </Select>
              <Button variant="ghost" size="icon" className="h-8 w-8" onClick={() => setOpen(false)} data-testid="button-close-chat">
                <X className="h-4 w-4" />
              </Button>
            </div>

  );
}


```

# Output

<img width="658" height="352" alt="image" src="https://github.com/user-attachments/assets/30a13452-c09c-480a-bd6a-76ac0334570e" />

<img width="658" height="331" alt="image" src="https://github.com/user-attachments/assets/aa02abd6-6173-4681-a76a-596b746e2937" />

<img width="624" height="410" alt="image" src="https://github.com/user-attachments/assets/b7b448e4-41dc-4164-9439-77c6cc9cc125" />

<img width="641" height="333" alt="image" src="https://github.com/user-attachments/assets/039378dc-914d-4b61-9af1-f5c9202602f4" />



# Result

The system accurately predicts crop prices using AI models and provides farmers with reliable selling guidance.Blockchain-based transactions ensure secure, transparent, and fraud-free payments. Overall, the platform improves accessibility, eliminates middlemen, and increases farmers’ profits through a smart digital marketplace.
