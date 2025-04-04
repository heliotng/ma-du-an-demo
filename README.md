import { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Button } from "@/components/ui/button";
import { Select, SelectItem } from "@/components/ui/select";
import { Label } from "@/components/ui/label";

const services = [
  { label: "Thẩm tra", code: "TT" },
  { label: "Thiết kế", code: "TK" },
  { label: "Lập dự toán", code: "DT" },
  { label: "Đấu thầu", code: "DTD" },
  { label: "BIM", code: "BIM" },
  { label: "Đào tạo", code: "DTao" },
  { label: "Nghiên cứu", code: "NC" },
  { label: "Lập dự án", code: "DA" },
];

let counter = 1; // for demo purpose only

export default function ProjectCodeGenerator() {
  const [projectName, setProjectName] = useState("");
  const [clientCode, setClientCode] = useState("");
  const [service, setService] = useState("");
  const [year] = useState(new Date().getFullYear().toString().slice(-2));
  const [month] = useState((new Date().getMonth() + 1).toString().padStart(2, "0"));
  const [code, setCode] = useState("");

  const generateCode = async () => {
    if (!clientCode || !service) return;
    const serviceCode = services.find((s) => s.label === service)?.code || "XX";
    const padded = counter.toString().padStart(3, "0");
    const generated = `${year}${clientCode.toUpperCase()}-${serviceCode}-${padded}`;
    setCode(generated);

    const payload = {
      projectName,
      clientCode: clientCode.toUpperCase(),
      service,
      year,
      month,
      counter,
      projectCode: generated,
    };

    try {
      await fetch("https://script.google.com/macros/s/AKfycbxLBK-iBECB4X9a88JyRn7eAQpqP402k4H55oAzi25TmizuzCovLGoBsDhqBIgv-4v_/exec", {
        method: "POST",
        mode: "no-cors",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(payload),
      });
    } catch (err) {
      console.error("Lỗi gửi dữ liệu:", err);
    }

    counter++;
  };

  return (
    <div className="max-w-xl mx-auto p-6 space-y-4">
      <h1 className="text-2xl font-bold">Tạo mã dự án</h1>
      <Card>
        <CardContent className="space-y-4 pt-6">
          <div>
            <Label>Tên dự án</Label>
            <Input value={projectName} onChange={(e) => setProjectName(e.target.value)} placeholder="VD: Cao ốc A" />
          </div>
          <div>
            <Label>Mã khách hàng (viết tắt)</Label>
            <Input value={clientCode} onChange={(e) => setClientCode(e.target.value)} placeholder="VD: VING, SUN" />
          </div>
          <div>
            <Label>Loại dịch vụ</Label>
            <Select onValueChange={setService} defaultValue="">
              {services.map((s) => (
                <SelectItem key={s.code} value={s.label}>{s.label}</SelectItem>
              ))}
            </Select>
          </div>
          <Button onClick={generateCode}>Tạo mã</Button>
          {code && (
            <div className="p-4 bg-green-100 rounded-xl text-green-800 font-semibold">
              Mã dự án: {code}
            </div>
          )}
        </CardContent>
      </Card>
    </div>
  );
}
