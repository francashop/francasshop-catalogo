import { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Select, SelectTrigger, SelectValue, SelectContent, SelectItem } from "@/components/ui/select";
import { Textarea } from "@/components/ui/textarea";
import { Plus, Trash, Lock, LogOut } from "lucide-react";

const initialSections = [
  "Scarpe",
  "T-shirt",
  "Completi",
  "Camice",
  "Pantaloni",
  "Short",
  "Mutande e Calzini",
  "Costumi",
];

const initialUtenti = [
  { username: "admin", password: "admin123" },
  { username: "mod1", password: "pass1" },
  { username: "mod2", password: "pass2" },
];

export default function CatalogoAbbigliamento() {
  const [sezioni, setSezioni] = useState(
    initialSections.map((s) => ({ nome: s, articoli: [] }))
  );
  const [activeSection, setActiveSection] = useState(initialSections[0]);
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [username, setUsername] = useState("");
  const [password, setPassword] = useState("");
  const [utenteLoggato, setUtenteLoggato] = useState(null);
  const [showLogin, setShowLogin] = useState(false);
  const [catalogTitle, setCatalogTitle] = useState("Catalogo Online - Abbigliamento");
  const [backgroundColor, setBackgroundColor] = useState("#ffffff");
  const [utentiAutorizzati, setUtentiAutorizzati] = useState(initialUtenti);
  const [newPassword, setNewPassword] = useState("");

  const handleLogin = () => {
    const utente = utentiAutorizzati.find(
      (u) => u.username === username && u.password === password
    );
    if (utente) {
      setIsLoggedIn(true);
      setUtenteLoggato(utente.username);
      setShowLogin(false);
    } else {
      alert("Credenziali errate");
    }
  };

  const handleLogout = () => {
    setIsLoggedIn(false);
    setUsername("");
    setPassword("");
    setUtenteLoggato(null);
  };

  const handlePasswordChange = () => {
    if (!newPassword) return;
    const updatedUsers = utentiAutorizzati.map((user) => {
      if (user.username === utenteLoggato) {
        return { ...user, password: newPassword };
      }
      return user;
    });
    setUtentiAutorizzati(updatedUsers);
    setNewPassword("");
    alert("Password aggiornata con successo!");
  };

  const addArticolo = (sectionIndex) => {
    const nuovoArticolo = {
      nome: "",
      descrizione: "",
      prezzo: "",
      colore: "",
      taglie: "",
      immagine: "",
    };
    const nuoveSezioni = [...sezioni];
    nuoveSezioni[sectionIndex].articoli.push(nuovoArticolo);
    setSezioni(nuoveSezioni);
  };

  const aggiornaArticolo = (sectionIndex, articleIndex, campo, valore) => {
    const nuoveSezioni = [...sezioni];
    nuoveSezioni[sectionIndex].articoli[articleIndex][campo] = valore;
    setSezioni(nuoveSezioni);
  };

  const rimuoviArticolo = (sectionIndex, articleIndex) => {
    const nuoveSezioni = [...sezioni];
    nuoveSezioni[sectionIndex].articoli.splice(articleIndex, 1);
    setSezioni(nuoveSezioni);
  };

  return (
    <div className="min-h-screen p-6" style={{ backgroundColor }}>
      <div className="flex justify-between items-center mb-6">
        {isLoggedIn ? (
          <Input
            className="text-3xl font-bold"
            value={catalogTitle}
            onChange={(e) => setCatalogTitle(e.target.value)}
          />
        ) : (
          <h1 className="text-3xl font-bold">{catalogTitle}</h1>
        )}
        {isLoggedIn && (
          <div className="flex items-center gap-4">
            <div className="flex items-center gap-2 text-green-700 bg-green-100 px-3 py-1 rounded-full">
              <Lock size={16} />
              <span className="text-sm font-medium">Modalità modifica attiva</span>
            </div>
            <Button variant="ghost" size="sm" onClick={handleLogout}>
              <LogOut size={16} className="mr-1" /> Esci
            </Button>
          </div>
        )}
      </div>

      {isLoggedIn && (
        <div className="mb-4 grid gap-4 max-w-md">
          <div>
            <label className="text-sm font-medium">Colore sfondo pagina</label>
            <Input
              type="color"
              value={backgroundColor}
              onChange={(e) => setBackgroundColor(e.target.value)}
              className="w-24 h-10 p-0 border-none"
            />
          </div>
          <div>
            <label className="text-sm font-medium">Nuova password ({utenteLoggato})</label>
            <div className="flex gap-2">
              <Input
                type="password"
                value={newPassword}
                onChange={(e) => setNewPassword(e.target.value)}
                placeholder="Inserisci nuova password"
              />
              <Button onClick={handlePasswordChange}>Aggiorna</Button>
            </div>
          </div>
        </div>
      )}

      <Select value={activeSection} onValueChange={setActiveSection}>
        <SelectTrigger className="w-[280px] mb-4">
          <SelectValue placeholder="Seleziona una categoria" />
        </SelectTrigger>
        <SelectContent>
          {sezioni.map((sezione, idx) => (
            <SelectItem key={idx} value={sezione.nome}>
              {sezione.nome}
            </SelectItem>
          ))}
        </SelectContent>
      </Select>

      {sezioni.map((sezione, sectionIndex) => (
        sezione.nome === activeSection && (
          <div key={sectionIndex} className="grid gap-4">
            {sezione.articoli.map((articolo, articleIndex) => (
              <Card key={articleIndex} className="relative">
                <CardContent className="grid gap-2 p-4">
                  <div>
                    <strong>Nome:</strong> {articolo.nome || "(non specificato)"}
                  </div>
                  <div>
                    <strong>Descrizione:</strong> {articolo.descrizione || "(non specificata)"}
                  </div>
                  <div>
                    <strong>Prezzo:</strong> {articolo.prezzo || "(non specificato)"} €
                  </div>
                  <div>
                    <strong>Colore:</strong> {articolo.colore || "(non specificato)"}
                  </div>
                  <div>
                    <strong>Taglie:</strong> {articolo.taglie || "(non specificate)"}
                  </div>
                  {articolo.immagine && (
                    <img
                      src={articolo.immagine}
                      alt={articolo.nome}
                      className="w-32 h-32 object-cover rounded border"
                    />
                  )}

                  {isLoggedIn && (
                    <div className="grid gap-2 mt-4">
                      <Input
                        placeholder="Nome articolo"
                        value={articolo.nome}
                        onChange={(e) =>
                          aggiornaArticolo(sectionIndex, articleIndex, "nome", e.target.value)
                        }
                      />
                      <Textarea
                        placeholder="Descrizione"
                        value={articolo.descrizione}
                        onChange={(e) =>
                          aggiornaArticolo(sectionIndex, articleIndex, "descrizione", e.target.value)
                        }
                      />
                      <Input
                        placeholder="Prezzo (€)"
                        value={articolo.prezzo}
                        onChange={(e) =>
                          aggiornaArticolo(sectionIndex, articleIndex, "prezzo", e.target.value)
                        }
                      />
                      <Input
                        placeholder="Colore"
                        value={articolo.colore}
                        onChange={(e) =>
                          aggiornaArticolo(sectionIndex, articleIndex, "colore", e.target.value)
                        }
                      />
                      <Input
                        placeholder="Taglie disponibili (es: S, M, L)"
                        value={articolo.taglie}
                        onChange={(e) =>
                          aggiornaArticolo(sectionIndex, articleIndex, "taglie", e.target.value)
                        }
                      />
                      <Input
                        placeholder="URL immagine"
                        value={articolo.immagine}
                        onChange={(e) =>
                          aggiornaArticolo(sectionIndex, articleIndex, "immagine", e.target.value)
                        }
                      />
                      <Button
                        variant="destructive"
                        size="sm"
                        className="absolute top-2 right-2"
                        onClick={() => rimuoviArticolo(sectionIndex, articleIndex)}
                      >
                        <Trash size={16} />
                      </Button>
                    </div>
                  )}
                </CardContent>
              </Card>
            ))}

            {isLoggedIn && (
              <Button
                className="w-full mt-2"
                variant="secondary"
                onClick={() => addArticolo(sectionIndex)}
              >
                <Plus className="mr-2" size={18} /> Aggiungi articolo
              </Button>
            )}
          </div>
        )
      ))}

      {showLogin && !isLoggedIn && (
        <div className="mt-6 grid gap-2 max-w-sm border p-4 rounded">
          <h2 className="text-lg font-semibold">Accesso Riservato</h2>
          <Input
            type="text"
            placeholder="Username"
            value={username}
            onChange={(e) => setUsername(e.target.value)}
          />
          <Input
            type="password"
            placeholder="Password"
            value={password}
            onChange={(e) => setPassword(e.target.value)}
          />
          <Button onClick={handleLogin}>Accedi</Button>
        </div>
      )}

      {!isLoggedIn && !showLogin && (
        <div className="mt-10 flex justify-center">
          <Button variant="outline" size="sm" onClick={() => setShowLogin(true)}>
            Accesso area riservata
          </Button>
        </div>
      )}
    </div>
  );
}
# francasshop-catalogo
