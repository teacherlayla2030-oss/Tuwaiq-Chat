# Tuwaiq-Chat

import type { Express } from "express";
import { createServer, type Server } from "http";
import { setupAuth } from "./auth";
import { storage } from "./storage";
import { generateChatResponse, generateVoiceResponse } from "./openai";

export async function registerRoutes(app: Express): Promise<Server> {
  setupAuth(app);

  app.post("/api/chat", async (req, res) => {
    if (!req.isAuthenticated()) return res.sendStatus(401);

    const { message, character } = req.body; 

    // إعداد شخصيات الرفقاء لمشروع طويق شات
    let systemPrompt = "";
    if (character === "najd") {
      systemPrompt = `أنتِ "الرفيقة نجد" في منصة طويق شات التعليمي. أستاذة ليلى هي معلمتكِ. ابدئي بعبارة: "أهلاً بكِ يا أستاذة ليلى، معكِ رفيقتكِ نجد". تحدثي بنبرة ملهمة وذكية.`;
    } else {
      systemPrompt = `أنت "الرفيق سعود" في منصة طويق شات التعليمي. أستاذة ليلى هي معلمتك. ابدأ بعبارة: "أهلاً بكِ يا أستاذة ليلى، معك رفيقك سعود". تحدث بنبرة طموحة تعكس طموح جبل طويق.`;
    }

    try {
      const aiResponse = await generateChatResponse(message, systemPrompt);
      const voiceBuffer = await generateVoiceResponse(aiResponse);
      const voiceBase64 = voiceBuffer.toString('base64');

      res.json({ text: aiResponse, audio: voiceBase64 });
    } catch (error) {
      console.error("Chat Error:", error);
      res.status(500).json({ message: "حدث خطأ في الاتصال بالرفيق" });
    }
  });

  const httpServer = createServer(app);
  return httpServer;
}
import type { Express } from "express";
import { createServer, type Server } from "http";
import { setupAuth } from "./auth";
import { storage } from "./storage";
import { generateChatResponse, generateVoiceResponse } from "./openai";

export async function registerRoutes(app: Express): Promise<Server> {
  setupAuth(app);

  app.post("/api/chat", async (req, res) => {
    if (!req.isAuthenticated()) return res.sendStatus(401);

    const { message, character } = req.body; 

    // تعريف شخصيات الرفقاء في طويق شات
    let systemPrompt = "";
    if (character === "najd") {
      systemPrompt = `أنتِ "الرفيقة نجد" في منصة طويق شات. المستخدمة هي أستاذة ليلى. ابدئي دائماً بـ: "أهلاً بكِ يا أستاذة ليلى، معكِ رفيقتكِ نجد". تحدثي بنبرة ملهمة وذكية.`;
    } else {
      systemPrompt = `أنت "الرفيق سعود" في منصة طويق شات. المستخدمة هي أستاذة ليلى. ابدأ دائماً بـ: "أهلاً بكِ يا أستاذة ليلى، معك رفيقك سعود". تحدث بنبرة طموحة كجبل طويق.`;
    }

    try {
      // توليد الرد النصي والصوتي
      const aiResponse = await generateChatResponse(message, systemPrompt);
      const voiceBuffer = await generateVoiceResponse(aiResponse);
      const voiceBase64 = voiceBuffer.toString('base64');

      res.json({ text: aiResponse, audio: voiceBase64 });
    } catch (error) {
      console.error("Chat Error:", error);
      res.status(500).json({ message: "حدث خطأ في الاتصال بالرفيق" });
    }
  });

  const httpServer = createServer(app);
  return httpServer;
}
