using HalakAPI.DTO;
using HalakAPI.Models;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;

namespace HalakAPI.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class HorgaszController : ControllerBase
    {
        [HttpGet("All")]
        public IActionResult All()
        {
            try
            {
                using (var cx = new HalakContext())
                {
                    var horgaszok = cx.Horgaszoks.Select(h => new Horgaszok
                    {
                        Id = h.Id,
                        Nev = h.Nev,
                        Eletkor = h.Eletkor
                    }).ToList();
                    return StatusCode(200, horgaszok);
                }
            }
            catch (Exception ex)
            {
                return StatusCode(400, ex.Message);
            }
        }

        [HttpGet("ById/{id}")]
        public IActionResult ById(int id)
        {
            try
            {
                using (var cx = new HalakContext())
                {
                    var horgaszok = cx.Horgaszoks.FirstOrDefault(h => h.Id == id);
                    
                    return StatusCode(200, horgaszok);
                }
            }
            catch (Exception ex)
            {
                return StatusCode(400, ex.Message);
            }
        }

        [HttpGet("FajMeretTo")]
        public IActionResult FajMeretTo()
        {
            try
            {
                using (var cx = new HalakContext())
                {

                    var halak = cx.Halaks.Select(h => new HalDTO
                    {
                        halFaj = h.Faj,
                        halMerete = h.MeretCm,
                        toNeve = h.To.Nev,
                    }).ToList();
                    return StatusCode(200, halak);
                }
            }
            catch (Exception ex)
            {
                return StatusCode(400, ex.Message);
            }
        }

        [HttpPost("Halak")]
        public IActionResult PostHal([FromBody] Halak dto, string UserID)
        {
            try
            {
                using (var cx = new HalakContext())
                {

                    if (UserID == Program.UID)
                    {
                        var hal = new Halak()
                        {
                            Faj = dto.Faj,
                            Nev = dto.Nev,
                            MeretCm = dto.MeretCm,
                            Kep = dto.Kep,
                            To = dto.To,
                        };
                        cx.Halaks.Add(hal);
                        cx.SaveChanges();
                        return StatusCode(200, "Hal hozzáadása sikeresen megtörtént.");
                    }
                    else
                    {
                        return StatusCode(401, "Nincs jogosultsága az új hal felvételéhez");
                    }
                    
                }
            }
            catch (Exception ex)
            {
                return StatusCode(400, ex.Message);
            }
        }

        [HttpPut("Halak={UserID}")]
        public IActionResult PostHal([FromBody] Halak dto, int id)
        {
            try
            {
                using (var cx = new HalakContext())
                {

                    var hal = cx.Halaks.FirstOrDefault(h=>h.Id == id);

                    if (hal != null)
                    {
                        var ujHal = new Halak()
                        {
                            Faj = dto.Faj,
                            Nev = dto.Nev,
                            MeretCm = dto.MeretCm,
                            Kep = dto.Kep,
                            To = dto.To,
                        };
                        cx.Halaks.Update(hal);
                        cx.SaveChanges();
                        return StatusCode(200, "Hal frissitése sikeresen megtörtént.");
                    }
                    else
                    {
                        return StatusCode(401, "Nincs ilyen hal.");
                    }

                }
            }
            catch (Exception ex)
            {
                return StatusCode(400, ex.Message);
            }
        }

        [HttpDelete("Halak/{id}")]
        public IActionResult PostHal(int id)
        {
            try
            {
                using (var cx = new HalakContext())
                {
                    var hal = cx.Halaks.FirstOrDefault(h => h.Id == id);
                    if (hal != null) {
                        cx.Halaks.Remove(hal);
                        return StatusCode(200, "Hal törlése sikeresen megtörtént.");
                    }
                    else
                    {
                        return StatusCode(401, "Nincs jogosultsága az új hal felvételéhez");
                    }

                }
            }
            catch (Exception ex)
            {
                return StatusCode(400, ex.Message);
            }
        }
    }
}
