// NEXT LEVEL AKSJEPLATTFORM (FULL VERSJON)
// Dette er en mer realistisk, proff versjon med:
// - Ekte API (Finnhub)
// - Flere aksjer (Oslo Børs eksempel)
// - Bedre UI
// - Enkel kjøp/selg analyse (RSI-ish logikk)

import React, { useEffect, useState } from "react";
import axios from "axios";
import { LineChart, Line, XAxis, YAxis, Tooltip, ResponsiveContainer } from "recharts";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";

const API_KEY = "DIN_FINNHUB_API_KEY";

const STOCKS = [
  { name: "Equinor", symbol: "EQNR.OL" },
  { name: "DNB", symbol: "DNB.OL" },
  { name: "Telenor", symbol: "TEL.OL" },
  { name: "Yara", symbol: "YAR.OL" }
];

export default function StockApp() {
  const [stocks, setStocks] = useState([]);
  const [selected, setSelected] = useState(null);
  const [premium, setPremium] = useState(false);

  useEffect(() => {
    fetchStocks();
  }, []);

  const fetchStocks = async () => {
    const results = await Promise.all(
      STOCKS.map(async (s) => {
        const res = await axios.get(
          `https://finnhub.io/api/v1/quote?symbol=${s.symbol}&token=${API_KEY}`
        );

        return {
          name: s.name,
          symbol: s.symbol,
          price: res.data.c,
          change: res.data.dp,
          history: generateFakeHistory(res.data.c)
        };
      })
    );

    setStocks(results);
  };

  // Midlertidig historikk (bytt senere med ekte candles API)
  const generateFakeHistory = (price) => {
    return Array.from({ length: 10 }, (_, i) => price - 5 + i);
  };

  // ENKEL ANALYSE (fake RSI-ish)
  const getRecommendation = (history) => {
    const diff = history[history.length - 1] - history[0];

    if (diff > 5) return "SELG";
    if (diff < -5) return "KJØP";
    return "HOLD";
  };

  return (
    <div className="p-6 grid gap-6 bg-black text-white min-h-screen">
      <h1 className="text-3xl font-bold">🚀 AksjePro</h1>

      {!premium && (
        <Card className="bg-gray-900">
          <CardContent className="p-4">
            <h2 className="text-xl font-semibold">Premium</h2>
            <p>Få analyser + kjøp/salg signaler</p>
            <div className="flex gap-3 mt-3">
              <Button onClick={() => alert("Koble Stripe Checkout her")}>30 kr / mnd</Button>
              <Button onClick={() => alert("Koble Stripe Checkout her")}>500 kr / år</Button>
            </div>
          </CardContent>
        </Card>
      )}

      <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
        {stocks.map((stock, i) => (
          <Card
            key={i}
            onClick={() => setSelected(stock)}
            className="cursor-pointer bg-gray-800"
          >
            <CardContent className="p-4">
              <h2 className="text-lg font-bold">{stock.name}</h2>
              <p>{stock.price} kr</p>
              <p className={stock.change > 0 ? "text-green-400" : "text-red-400"}>
                {stock.change}%
              </p>
            </CardContent>
          </Card>
        ))}
      </div>

      {selected && (
        <Card className="bg-gray-900">
          <CardContent className="p-4">
            <h2 className="text-xl font-bold">{selected.name}</h2>

            <ResponsiveContainer width="100%" height={300}>
              <LineChart data={selected.history.map((v, i) => ({ i, v }))}>
                <XAxis dataKey="i" />
                <YAxis />
                <Tooltip />
                <Line type="monotone" dataKey="v" />
              </LineChart>
            </ResponsiveContainer>

            {premium ? (
              <div className="mt-4 text-lg">
                📊 Anbefaling: {getRecommendation(selected.history)}
              </div>
            ) : (
              <p className="mt-4">🔒 Kjøp premium for analyser</p>
            )}
          </CardContent>
        </Card>
      )}
    </div>
  );
}

/*
