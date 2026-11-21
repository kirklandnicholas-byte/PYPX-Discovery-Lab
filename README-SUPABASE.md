# PYPX Discovery Lab — Netlify + Supabase

This edition replaces the old local Node server with a Supabase Edge Function. The web planner and AI Inquiry Coach are preserved. **Do not deploy `server.js`: it is from the previous local edition.**

## One-time setup
1. Create a **separate** Supabase project for PYPX. Open **SQL Editor** and run `supabase/setup.sql` once.
2. Go to **Edge Functions** and create a function named `pypx-ai`. Paste `supabase/functions/pypx-ai/index.ts` into its editor, then deploy. In the function settings, turn **Verify JWT** OFF. The function instead checks the server-held classroom code; it is intended for a supervised pilot.
3. In Supabase Edge Function **Secrets**, set:
   - `OPENAI_API_KEY` = your private OpenAI API key
   - `PYPX_CLASSROOM_CODE` = a unique student classroom code (use 12+ characters)
   - `PYPX_TEACHER_PIN` = a different strong teacher-only PIN (12+ characters)
   - `PYPX_ALLOWED_ORIGIN` = your exact Netlify site origin, e.g. `https://pypx-discovery-lab.netlify.app` (no trailing slash). Add comma-separated exact origins only if necessary.
   - `PYPX_DAILY_LIMIT` = `40`
   - `OPENAI_MODEL` = `gpt-4.1-mini`
   Supabase automatically supplies `SUPABASE_URL` and `SUPABASE_SERVICE_ROLE_KEY` in most hosted projects; verify in Function settings. Never put secrets in GitHub or `public` code.
4. In `public/index.html`, replace `__SUPABASE_FUNCTION_URL__` with the exact deployed function URL, e.g. `https://PROJECTREF.supabase.co/functions/v1/pypx-ai`. This URL is public, not a secret.
5. Upload the repository contents to GitHub and connect it to Netlify. Set Netlify publish directory to **`public`**. The `netlify.toml` already sets this.
6. Open the website. Expand Teacher AI controls, type teacher PIN to switch AI **ON**; enter the student classroom code and press **Connect AI**. Try a research request and an Inquiry Coach draft. Students only need the classroom code; never share the teacher PIN.

## Important limitations
- The classroom code is a shared password, not individual student authentication. The global daily quota is enforced atomically in Postgres but is **not** an exact financial cap; API costs depend on tokens and web searches. Do not distribute publicly without per-student authentication, abuse protection, moderation, and school data-protection review.
- Browser origin filtering only restricts browser access and is not authentication; code sharing remains possible. Do not enter student names or personal information.
- AI-off and quota data persist in Supabase; the quota resets on UTC day boundaries.
- OpenAI API and Supabase/Netlify accessibility from your school network in mainland China must be tested.
- No OpenAI calls are possible until you deploy/configure the function. The offline planner remains available.
- `server.js`, `.env.example`, and the OS launch scripts are older local-server files retained for reference, not part of the Netlify build.
